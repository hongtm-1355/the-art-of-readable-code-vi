# CHAPTER SIX: Making Comments Precise and Compact

![](https://i.imgur.com/dToTytG.png)

The previous chapter was about realizing what you should be commenting. This chapter is about how to write comments that are precise and compact.

If you're going to write a comment at all, it might as well be precise—as specific and detailed as possible. On the other hand, comments take up extra space on the screen, and take extra time to read. So comments should also be compact.

> K E Y I D E A
> Comments should have a high information-to-space ratio.

The rest of the chapter shows examples of how to do this.

## Keep Comments Compact

Here’s an example of a comment for a C++ type definition:
```
// The int is the CategoryType.
// The first float in the inner pair is the 'score',
// the second is the 'weight'.
typedef hash_map<int, pair<float, float> > ScoreMap;
```
But why use three lines to explain it, when you can illustrate it in just one line?
```
// CategoryType -> (score, weight)
typedef hash_map<int, pair<float, float> > ScoreMap;
```

Some comments need three lines of space, but this is not one of them.

## Avoid Ambiguous Pronouns

As the classic “Who’s on First?” skit illustrated, pronouns can make things very confusing. 

It takes extra work for the reader to “resolve” a pronoun. And in some cases, it’s unclear what “it” or “this” is referring to. Here’s an example:
```
// Insert the data into the cache, but check if it's too big first.
```
In this comment, “it” might refer to the data or the cache. You could probably figure that out by reading the rest of the code. But if you have to do that, what’s the point of the comment?

The safest thing is to “fill in” pronouns if there’s any chance of confusion. In the previous example, let’s assume “it” was “the data”:
```
// Insert the data into the cache, but check if the data is too big first.
```
This is the simplest change to make. You could also have restructured the sentence to make “it” perfectly clear:
```
// If the data is small enough, insert it into the cache.
```

## Polish Sloppy Sentences

In many cases, making a comment more precise goes hand-in-hand with making it more compact.

Here is an example from a web crawler:
```
# Depending on whether we've already crawled this URL before, give it a different priority.
```
This sentence might seem okay, but compare it to this version:
```
# Give higher priority to URLs we've never crawled before.
```
This sentence is simpler, smaller, and more direct. It also explains that higher priority is given to uncrawled URLs—the previous comment didn’t contain that information

## Describe Function Behavior Precisely

Imagine you just wrote a function that counts the number of lines in a file:
```
// Return the number of lines in this file.
int CountLines(string filename) { ... }
```
This comment isn’t very precise—there are a lot of ways to define a “line.” Here are some corner cases to think about:
• "" (an empty file)—0 or 1 line?
• "hello"—0 or 1 line?
• "hello\n"—1 or 2 lines?
• "hello\n world"—1 or 2 lines?
• "hello\n\r cruel\n world\r"—2, 3, or 4 lines?
The simplest implementation is to count the number of newline (\n) characters. (This is the way the Unix command wc works.) Here’s a better comment to match this implementation:
```
// Count how many newline bytes ('\n') are in the file.
int CountLines(string filename) { ... }
```
This comment isn’t much longer than the first version, but contains much more information.

It tells the reader that the function might return 0 if there are no newlines. It also tells the reader that carriage returns (\r) are ignored.

## Use Input/Output Examples That Illustrate Corner Cases

When it comes to comments, a carefully chosen input/output example can be worth a thousand words.

For example, here’s a common function that removes parts of a string:
```
// Remove the suffix/prefix of 'chars' from the input 'src'.
String Strip(String src, String chars) { ... }
```
This comment isn’t very precise because it can’t answer questions such as:
- Is chars a whole substring that is to be removed, or effectively just an unordered set of
letters?
- What if there are multiples of chars on the end of src?

Instead, a well-chosen example can answer these questions:
```
// ...
// Example: Strip("abba/a/ba", "ab") returns "/a/"
String Strip(String src, String chars) { ... }
```
The example “shows off” the full functionality of Strip(). Note that a simpler example wouldn’t be as useful, if it doesn’t answer those questions:
```
// Example: Strip("ab", "a") returns "b"
```
Here’s another example of a function that could use an illustration:
```
// Rearrange 'v' so that elements < pivot come before those >= pivot;
// Then return the largest 'i' for which v[i] < pivot (or -1 if none are < pivot)
int Partition(vector<int>* v, int pivot);
```
This comment is actually very precise, but a little bit hard to visualize. Here’s an example you could include to illustrate things further:
```
// ...
// Example: Partition([8 5 9 8 2], 8) might result in [5 2 | 8 9 8] and return 1
int Partition(vector<int>* v, int pivot);
```

There are a number of points to mention about the specific example input/output we chose:
- The pivot is equal to elements in the vector to illustrate that edge case.
- We put duplicates in the vector (8) to illustrate that this is an acceptable input.
- The resulting vector is not sorted—if it were, the reader might get the wrong idea.
- Because the return value was 1, we made sure 1 wasn’t also a value in the vector—that would be confusing.

## State the Intent of Your Code

As we mentioned in the previous chapter, commenting is often about telling the reader what you were thinking about when you wrote the code. Unfortunately, many comments end up just describing what the code does in literal terms, without adding much new information.

Here’s an example of such a comment:
```
void DisplayProducts(list<Product> products) {
    products.sort(CompareProductByPrice);
    // Iterate through the list in reverse order
        for (list<Product>::reverse_iterator it = products.rbegin(); it != products.rend();
             ++it)
            DisplayPrice(it->price);
    ...
}
```
All this comment does is just describe the line below it. Instead, consider this better comment:
```
// Display each price, from highest to lowest
for (list<Product>::reverse_iterator it = products.rbegin(); ... )
```

This comment explains what the program is doing at a higher level. This is much more in tune with what the programmer was thinking when she wrote the code.

Interestingly, there is a bug in this program! The CompareProductByPrice function (not shown) already sorts higher-priced items first. The code is doing the opposite of what the author intended.

This is a good reason why the second comment is better. Despite the bug, the first comment is technically correct (the loop does iterate in reverse order). But with the second comment, a reader is more likely to notice that the intent of the writer (to show higher-priced items first) contradicts what the code actually does. In effect, the comment acts as a redundancy check.

Ultimately, the best redundancy check is a unit test (see Chapter 14, Testing and Readability). But it’s still worthwhile having comments like these explaining the intent of your program.

## “Named Function Parameter” Comments
Suppose you saw a function call like this one:
```
Connect(10, false);
```
This function call is a bit mysterious because of those integer and boolean literals being passed in.

In languages like Python, you can assign the arguments by name:

```
def Connect(timeout, use_encryption): ...
# Call the function using named parameters
Connect(timeout = 10, use_encryption = False)
```
In languages like C++ and Java, you can’t do this. However, you can use an inline comment to the same effect:

```
void Connect(int timeout, bool use_encryption) { ... }
// Call the function with commented parameters
Connect(/* timeout_ms = */ 10, /* use_encryption = */ false);
```
Notice that we “named” the first parameter timeout_ms instead of timeout. Ideally, the function’s real argument would have been timeout_ms, but if for some reason we can’t make this change, this is a handy way to “improve” the name.

When it comes to boolean arguments, it’s especially important to put /* name = */ in front of the value. Putting the comment behind the value is very confusing:

```
// Don't do this!
Connect( ... , false /* use_encryption */);
// Don't do this either!
Connect( ... , false /* = use_encryption */);
```
In these examples, it’s unclear whether false means “use encryption” or “don’t use encryption”.

Most function calls don’t need comments like these, but it’s a handy (and compact) way to explain mysterious-looking arguments.

## Use Information-Dense Words

Once you’ve been programming for a number of years, you notice that the same general problems and solutions come up repeatedly. Often, there are specific words or phrases that have been developed to describe these patterns/idioms. Using these words can make your comments much more compact.

For example, suppose your comment were:
```
// This class contains a number of members that store the same information as in the
// database, but are stored here for speed. When this class is read from later, those
// members are checked first to see if they exist, and if so are returned; otherwise the
// database is read from and that data stored in those fields for next time.
```
Instead, you could just say:
```
// This class acts as a caching layer to the database.
```
As another example, a comment such as:
```
// Remove excess whitespace from the street address, and do lots of other cleanup
// like turn "Avenue" into "Ave." This way, if there are two different street addresses
// that are typed in slightly differently, they will have the same cleaned-up version and
// we can detect that these are equal. 
```
could instead be:
```
// Canonicalize the street address (remove extra spaces, "Avenue" -> "Ave.", etc.)
```
There are lots of words and phrases that pack a lot of meaning, such as “heuristic,” “bruteforce,” “naive solution,” and the like. If you have a comment that feels a bit long-winded, see if it can be described as a typical programming situation

## Summary
This chapter is about writing comments that pack as much information into as small a space as possible. Here are the specific tips:
- Avoid pronouns like “it” and “this” when they can refer to multiple things.
- Describe a function’s behavior with as much precision as is practical.
- Illustrate your comments with carefully chosen input/output examples.
- State the high-level intent of your code, rather than the obvious details.
- Use inline comments (e.g., Function(/* arg = */ ... ) ) to explain mysterious function arguments.
- Keep your comments brief by using words that pack a lot of meaning






















