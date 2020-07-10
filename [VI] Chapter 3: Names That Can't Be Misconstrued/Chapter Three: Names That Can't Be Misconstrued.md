# Chapter Three: Names That Can't Be Misconstrued
![](https://i.imgur.com/pynFtTu.png)

In the previous chapter, we covered how to put a lot of information into your names. In this chapter, we focus on a different topic: watching out for names that can be misunderstood.

> KEY IDEA
> Actively scrutinize your names by asking yourself, “What other meanings could someone interpret from this name?”
>

Really try to be creative here, actively seeking “wrong interpretations.” This step will help you spot those ambiguous names so you can change them

For the examples in this chapter, we’re going to “think aloud” as we discuss the misinterpretations of each name we see, and then pick better names

## Example: Filter()

Suppose you’re writing code to manipulate a set of database results:

```
results = Database.all_objects.filter("year <= 2011")
```
What does results now contain?
- Objects whose year is <= 2011?
- Objects whose year is not <= 2011?

The problem is that filter is an ambiguous word. It’s unclear whether it means “to pick out” or “to get rid of.” It’s best to avoid the name filter because it’s so easily misconstrued.
If you want “to pick out,” a better name is select(). If you want “to get rid of,” a better name is exclude().

## Example: Clip(text, length)
Suppose you have a function that clips the contents of a paragraph:

```python
# Cuts off the end of the text, and appends "..."
def Clip(text, length):
...
```

There are two ways you can imagine how Clip() behaves:
- It removes length from the end
- It truncates to a maximum length

The second way (truncation) is most likely, but you never know for sure. Rather than leave your reader with any nagging doubt, it would be better to name the function Truncate(text,length)

However, the parameter name length is also to blame. If it were max_length, that would make it even more clear.
But we’re still not done. The name max_length still leaves multiple interpretations:
- A number of bytes
- A number of characters
- A number of words
As you saw in the previous chapter, this is a case where the units should be attached to the name. In this case, we mean “number of characters,” so instead of max_length, it should be max_chars.

## Prefer min and max for (Inclusive) Limits

Let’s say your shopping cart application needs to stop people from buying more than 10 items at once:
```
CART_TOO_BIG_LIMIT = 10

if shopping_cart.num_items() >= CART_TOO_BIG_LIMIT:
    Error("Too many items in cart.")
```
This code has a classic off-by-one bug. We could easily fix it by changing >= to >:
```
if shopping_cart.num_items() > CART_TOO_BIG_LIMIT:
```
(or by redefining CART_TOO_BIG_LIMIT to 11). But the root problem is that CART_TOO_BIG_LIMIT is an ambiguous name—it’s not clear whether you mean “up to” or “up to and including.”

> ADVICE
> The clearest way to name a limit is to put max_ or min_ in front of the thing being limited

In this case, the name should be MAX_ITEMS_IN_CART. The new code is simple and clear:

```
MAX_ITEMS_IN_CART = 10

if shopping_cart.num_items() > MAX_ITEMS_IN_CART:
    Error("Too many items in cart.")
```

## Prefer first and last for Inclusive Ranges

![](https://i.imgur.com/ZasRs15.png)

Here is another example where you can’t tell if it’s “up to” or “up to and including”:
```
print integer_range(start=2, stop=4)
# Does this print [2,3] or [2,3,4] (or something else)?
```
Although start is a reasonable parameter name, stop can be interpreted in multiple ways here.
For inclusive ranges likes these (where the range should include both end points), a good choice is first/last. For instance:
```
set.PrintKeys(first="Bart", last="Maggie")
```
Unlike stop, the word last is clearly inclusive.
In addition to first/last, the names min/max may also work for inclusive ranges, assuming they “sound right” in that context.

## Prefer begin and end for Inclusive/Exclusive Ranges

![](https://i.imgur.com/4PDMKAu.png)

In practice, it’s often more convenient to use inclusive/exclusive ranges. For example, if you want to print all the events that happened on October 16, it’s easier to write:
```
PrintEventsInRange("OCT 16 12:00am", "OCT 17 12:00am")
```
than it is to write:
```
PrintEventsInRange("OCT 16 12:00am", "OCT 16 11:59:59.9999pm")
```
So what is a good pair of names for these parameters? Well, the typical programming convention for naming an inclusive/exclusive range is begin/end.
But the word end is a little ambiguous. For example, in the sentence, “I’m at the end of the book,” the “end” is inclusive. Unfortunately, English doesn’t have a succinct word for “just past the last value.

Because begin/end is so idiomatic (at least, it’s used this way in the standard library for C++, and most places where an array needs to be “sliced” this way), it’s the best option.

## Naming Booleans

When picking a name for a boolean variable or a function that returns a boolean, be sure it’s clear what true and false really mean.
Here’s a dangerous example:
```
bool read_password = true;
```
Depending on how you read it (no pun intended), there are two very different interpretations:
- We need to read the password
- The password has already been read
In this case, it’s best to avoid the word “read,” and name it need_password or user_is_authenticated instead.

In general, adding words like is, has, can, or should can make booleans more clear.
For example, a function named SpaceLeft() sounds like it might return a number. If it were meant to return a boolean, a better name would be HasSpaceLeft().
Finally, it’s best to avoid negated terms in a name. For example, instead of:
```
bool disable_ssl = false;
```
it would be easier to read (and more compact) to say:
```
bool use_ssl = true;
```
## Matching Expectations of Users
Some names are misleading because the user has a preconceived idea of what the name means, even though you mean something else. In these cases, it’s best to just “give in” and change the name so that it’s not misleading.
### Example: get*()
Many programmers are used to the convention that methods starting with get are “lightweight accessors” that simply return an internal member. Going against this convention is likely to mislead those users.

Here’s an example, in Java, of what not to do:
```
public class StatisticsCollector {
    public void addSample(double x) { ... }
        public double getMean() {
            // Iterate through all samples and return total / num_samples
        }
        ...
    }
```
In this case, the implementation of getMean() is to iterate over past data and calculate the mean on the fly. This step might be very expensive if there’s a lot of data! But an unsuspecting programmer might call getMean() carelessly, assuming that it’s an inexpensive call.

Instead, the method should be renamed to something like computeMean(), which sounds more like an expensive operation. (Alternatively, it should be reimplemented to indeed be a lightweight operation.)

### Example: list::size()

Here’s an example from the C++ Standard Library. The following code was the cause of a very difficult-to-find bug that made one of our servers slow down to a crawl:
```
void ShrinkList(list<Node>& list, int max_size) {
    while (list.size() > max_size) {
        FreeNode(list.back());
        list.pop_back();
    }
}
```
The “bug” is that the author didn’t know that list.size() is an O(n) operation—it counts through the linked list node by node, instead of just returning a precalculated count, which makes ShrinkList() an O(n2) operation.

The code is technically “correct,” and in fact passed all our unit tests. But when ShrinkList() was called on a list with a million elements, it took over an hour to finish!

Maybe you’re thinking, “That’s the caller’s fault—he or she should have read the documentation more carefully.” That’s true, but in this case, the fact that list.size() isn’t a constant-time operation is surprising. All of the other containers in C++ have a constant-time size() method.

Had size() been named countSize() or countElements(), the same mistake would be less likely. The writers of the C++ Standard Library probably wanted to name the method size() to match all the other containers like vector and map. But because they did, programmers easily mistake it to be a fast operation, the way it is for other containers. Thankfully, the latest C++ standard now mandates size() to be O(1).

> WHO’S THE WIZARD?
> A while ago, one of the authors was installing the OpenBSD operating system. During the disk formatting step, a complicated menu appeared, asking for disk parameters. One of the options was to go to “Wizard mode.” He was relieved to find this user-friendly option and selected it. To his dismay, it dropped the installer into a low-level prompt waiting for manual disk formatting commands, with no clear way to get out of it. Evidently “wizard” meant you were the wizard!

## Example: Evaluating Multiple Name Candidates

When deciding on a good name, you might have multiple candidates that you’re considering. It’s common to debate the merits of each name in your head before settling on the final choice. The following example illustrates this critiquing process.

High-traffic websites often use “experiments” to test whether a change to the website improves business. Here’s an example of a config file that controls some experiments:
```
experiment_id: 100
description: "increase font size to 14pt"
traffic_fraction: 5%
...
```

Each experiment is defined by about 15 attribute/value pairs. Unfortunately, when defining another experiment that’s very similar, you have to copy and paste most of those lines:
```
experiment_id: 101
description: "increase font size to 13pt"
[other lines identical to experiment_id 100]
```
Suppose we want to fix this situation by introducing a way to have one experiment reuse the properties from another. (This is the "prototype inheritance" pattern.) The end result is that you would type something like:
```
experiment_id: 101
the_other_experiment_id_I_want_to_reuse: 100
[change any properties as needed]
```
The question is: what should the_other_experiment_id_I_want_to_reuse really be named?
Here are four names to consider:
1. template
2. reuse
3. copy
4. inherit

Any of these names make sense to us because we’re the ones adding this new feature to the config language. But we have to imagine how the name will sound to someone who comes across the code and doesn’t know about this feature. So let’s analyze each name, thinking of ways someone could misinterpret it.

1. Let’s imagine using the name template:
```
experiment_id: 101
template: 100
...
```
template has a couple problems. First, it’s not clear whether it’s saying “I am a template” or “I am using this other template.” Second, a “template” is often something abstract that must be “filled in” before it is concrete. Someone might think a templated experiment isn’t a “real” experiment. Overall, template is just too vague in this situation.

2. How about reuse:
```
experiment_id: 101
reuse: 100
...
```
reuse is an okay word, but as written, someone might think it’s saying, “This experiment can be reused at most 100 times.” Changing the name to reuse_id would help. But a confused reader might think reuse_id: 100 means “my id for reuse is 100.”

3. Let’s consider copy.
```
experiment_id: 101
copy: 100
...
```
copy is a good word. But by itself, copy: 100 seems like it might be saying “copy this experiment 100 times” or “this is the 100th copy of something.” To make it clear that this term refers to another experiment, we could change the name to copy_experiment. This is probably the best
name so far.

4. But now let’s consider inherit:
```
experiment_id: 101
inherit: 100
...
```
The word “inherit” is familiar to most programmers, and it’s understood that further modifications are made after inheritance. With class inheritance, you get all the methods and members of another class and then modify them or add more. Even in real life, when you inherit possessions from a relative, it’s understood that you might sell them or own other things yourself.

But again, let’s make it clear that we’re inheriting from another experiment. We can improve the name to inherit_from or even inherit_from_experiment_id

Overall, copy_experiment and inherit_from_experiment_id are the best names, because they most clearly describe what’s happening and are least likely to be misunderstood.

## Summary
The best names are ones that can’t be misconstrued—the person reading your code will understand it the way you meant it, and no other way. Unfortunately, a lot of English words are ambiguous when it comes to programming, such as filter, length, and limit.

Before you decide on a name, play devil’s advocate and imagine how your name might be misunderstood. The best names are resistant to misinterpretation.

When it comes to defining an upper or lower limit for a value, max_ and min_ are good prefixes to use. For inclusive ranges, first and last are good. For inclusive/exclusive ranges, begin and end are best because they’re the most idiomatic.

When naming a boolean, use words like is and has to make it clear that it’s a boolean. Avoid negated terms (e.g., disable_ssl).

Beware of users’ expectations about certain words. For example, users may expect get() or size() to be lightweight methods.
