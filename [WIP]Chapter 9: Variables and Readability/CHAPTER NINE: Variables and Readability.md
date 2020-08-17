# CHAPTER NINE: Variables and Readability

![](https://i.imgur.com/RD34tXo.png)

In this chapter, you’ll see how sloppy use of variables makes a program harder to understand.

Specifically, there are three problems to contend with:
    1. The more variables there are, the harder it is to keep track of them all.
    2. The bigger a variable’s scope, the longer you have to keep track of it.
    3. The more often a variable changes, the harder it is to keep track of its current value.
    
The next three sections discuss how to deal with these issues.

## Eliminating Variables
In Chapter 8, Breaking Down Giant Expressions, we showed how introducing "explaining” or “summary” variables can make code more readable. These variables were helpful because they broke down giant expressions and acted as a form of documentation.

In this section, we’re interested in eliminating variables that don’t improve readability. When a variable like this is removed, the new code is more concise and just as easy to understand.

In the following section are a few examples of how these unnecessary variables show up.

### Useless Temporary Variables
In the following snippet of Python code, consider the now variable:
```
now = datetime.datetime.now()
root_message.last_view_time = now
```
Is now a variable worth keeping? No, and here are the reasons:

- It isn’t breaking down a complex expression.
- It doesn’t add clarification—the expression datetime.datetime.now() is clear enough.
- It’s used only once, so it doesn’t compress any redundant code.
Without now, the code is just as easy to understand:
```
root_message.last_view_time = datetime.datetime.now()
```
Variables like now are usually “leftovers” that remain after code has been edited. The variable now might have been used in multiple places originally. Or maybe the coder anticipated using now multiple times, but never actually needed it.

### Eliminating Intermediate Results
![](https://i.imgur.com/di0hmzj.png)
```javascript=
var remove_one = function (array, value_to_remove) {
    var index_to_remove = null;
    for (var i = 0; i < array.length; i += 1) {
        if (array[i] === value_to_remove) {
            index_to_remove = i;
            break;
        }
    }
    if (index_to_remove !== null) {
        array.splice(index_to_remove, 1);
    }
};
```
The variable index_to_remove is just used to hold an intermediate result. Variables like this can sometimes be eliminated by handling the result as soon as you get it:
```javascript=
var remove_one = function (array, value_to_remove) {
    for (var i = 0; i < array.length; i += 1) {
        if (array[i] === value_to_remove) {
            array.splice(i, 1);
            return;
        }
    }
};
```
By allowing the code to return early, we got rid of index_to_remove altogether and simplified the code quite a bit.

In general, it’s a good strategy to complete the task as quickly as possible.

### Eliminating Control Flow Variables

Sometimes you’ll see this pattern of code in loops:

```javascript=
boolean done = false;
    while (/* condition */ && !done) {
    ...
    if (...) {
        done = true;
        continue;
    }
}

```

The variable done might even be set to true in multiple places throughout the loop.

Code like this is often trying to satisfy some unspoken rule that you shouldn’t break out of the middle of a loop. There is no such rule!

Variables like done are what we call “control flow variables.” Their sole purpose is to steer the program’s execution—they don’t contain any real program data. In our experience, control flow variables can often be eliminated by making better use of structured programming:

```
while (/* condition */) {
    ...
    if (...) {
        break;
    }
}
```
This case was pretty easy to fix, but what if there are multiple nested loops for which a simple break wouldn’t suffice? In more complicated cases like that, the solution often involves moving code into a new function (either the code inside the loop, or the entire loop itself)

> DO YOU WANT YOUR COWORKERS TO FEEL LIKE
> THEY’RE IN AN INTERVIEW ALL THE TIME?
> Microsoft’s Eric Brechner has talked about how a great interview question should involve at least three variables.* It’s probably because dealing with three variables at the same time forces you to think hard! This makes sense for an interview, where you’re trying to push a candidate to the limit.
> But do you want your coworkers to feel like they’re in an interview while they’re reading your code?

## Shrink the Scope of Your Variables

We’ve all heard the advice to “avoid global variables.” This is good advice, because it’s hard to keep track of where and how all those global variables are being used. And by “polluting the namespace” (putting a bunch of names there that might conflict with your local variables), code might accidentally modify a global variable when it intended to use a local variable, or vice versa.

In fact, it’s a good idea to “shrink the scope” of all your variables, not just the global ones

> KEY IDEA
> Make your variable visible by as few lines of code as possible.

Many programming languages offer multiple scope/access levels, including module, class,
function, and block scope. Using more restricted access is generally better because it means the variable can be “seen” by fewer lines of code.

Why do this? Because it effectively reduces the number of variables the reader has to think about at the same time. If you were to shrink the scope of all your variables by a factor of two, then on average there would be half as many variables in scope at any one time.

For example, suppose you have a very large class, with a member variable that’s used by only two methods, in the following way

```
class LargeClass {
    string str_;
    void Method1() {
        str_ = ...;
        Method2();
    }
    void Method2() {
        // Uses str_
    }
    // Lots of other methods that don't use str_ ...
};

```
In some sense, a class member variable is like a “mini-global” inside the realm of the class. For large classes especially, it’s hard to keep track of all the member variables and which methods modify each one. The fewer mini-globals, the better.

For this case, it may make sense to “demote” str_ to be a local variable:

```
class LargeClass {
    void Method1() {
        string str = ...;
        Method2(str);
    }
    void Method2(string str) {
        // Uses str
    }
    // Now other methods can't see str.
};
```
Another way to restrict access to class members is to make as many methods static as possible. Static methods are a great way to let the reader know “these lines of code are isolated from those variables.”

Or another approach is to break the large class into smaller classes. This approach is helpful only if the smaller classes are in fact isolated from each other. If you were to create two classes that access each other’s members, you haven’t really accomplished anything.

The same goes for breaking up large files into smaller files or large functions into smaller functions. A big motivation for doing so is to isolate data (i.e., variables).

But different languages have different rules for what exactly constitutes a scope. We’d like to point out just a few of the more interesting rules involving the scope of variables. 

### if Statement Scope in C++

Suppose you have the following C++ code:

```
PaymentInfo* info = database.ReadPaymentInfo();
    if (info) {
        cout << "User paid: " << info->amount() << endl;
    }
// Many more lines of code below ...
```
The variable info will remain in scope for the rest of the function, so the person reading this code might keep info in mind, wondering if/how it will be used again.

But in this case, info is only used inside the if statement. In C++, we can actually define info in the conditional expression:

```
if (PaymentInfo* info = database.ReadPaymentInfo()) {
    cout << "User paid: " << info->amount() << endl;
}
```
Now the reader can easily forget about info after it goes out of scope

## Creating “Private” Variables in JavaScript

Suppose you have a persistent variable that’s used by only one function:

```
submitted = false; // Note: global variable
var submit_form = function (form_name) {
    if (submitted) {
        return; // don't double-submit the form
    }
    ...
    submitted = true;
};
```

Global variables like submitted can cause the person reading this code a lot of angst. It seems like submit_form() is the only function that uses submitted, but you can’t know for sure. In fact, another JavaScript file might be using a global variable named submitted too, for a different purpose!

You can prevent this issue by wrapping submitted inside a closure:

```javascript=
var submit_form = (function () {
    var submitted = false; // Note: can only be accessed by the function below
    return function (form_name) {
        if (submitted) {
                return; // don't double-submit the form
            }
            ...
        submitted = true;
        };
}());

```
Note the parentheses on the last line—the anonymous outer function is immediately executed, returning the inner function.

If you haven’t seen this technique before, it may look strange at first. It has the effect of making a “private” scope that only the inner function can access. Now the reader doesn’t have to wonder Where else does submitted get used? or worry about conflicting with other globals of the same name. (See JavaScript: The Good Parts by Douglas Crockford [O’Reilly, 2008] for more techniques like this.)

### JavaScript Global Scope

In JavaScript, if you omit the keyword var from a variable definition (e.g., x = 1 instead of var x = 1), the variable is put into the global scope, where every JavaScript file and `<script>` block can access it. Here is an example:

```
<script>
var f = function () {
    // DANGER: 'i' is not declared with 'var'!
    for (i = 0; i < 10; i += 1) ...
};
f();
</script>
```

This code inadvertently puts i into the global scope, so a later block can still see it:
```
<script>
    alert(i); // Alerts '10'. 'i' is a global variable!
</script>
```

Many programmers aren’t aware of this scoping rule, and this surprising behavior can cause strange bugs. A common manifestation of this bug is when two functions both create a local variable with the same name, but forget to use var. These functions will unknowingly “cross-talk” and the poor programmer might conclude that his computer is possessed or that his RAM has gone bad.

The common “best practice” for JavaScript is to always define variables using the var keyword (e.g., var x = 1). This practice limits the scope of the variable to the (innermost) function where it’s defined.

## No Nested Scope in Python and JavaScript

Languages like C++ and Java have block scope, where variables defined inside an if, for, try, or similar structure are confined to the nested scope of that block:

```
if (...) {
    int x = 1;
}
x++; // Compile-error! 'x' is undefined.
```

But in Python and JavaScript, variables defined in a block “spill out” to the whole function. For example, notice the use of example_value in this perfectly valid Python code:

```
# No use of example_value up to this point.
if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break
for logger in debug.loggers:
    logger.log("Example:", example_value)
```
This scoping rule is surprising to many programmers, and code like this is harder to read. In other languages, it would be easier to find where example_value was first defined—you would look along the “left-hand edge” of the function you’re inside.

The previous example is also buggy: if example_value is not set in the first part of the code, the second part will raise an exception: "NameError: ‘example_value’ is not defined". We can fix this, and make the code more readable, by defining example_value at the “closest common ancestor” (in terms of nesting) to where it’s used:

```python=
example_value = None

if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break
if example_value:
    for logger in debug.loggers:
        logger.log("Example:", example_value)
```
However, this is a case where example_value can be eliminated altogether. example_value is just holding an intermediate result, and as we saw in “Eliminating Intermediate Results” on page 95, variables like these can often be eliminated by “completing the task as soon as possible.” In this case, that means logging the example value as soon as we find it.

Here’s what the new code looks like:

```python=
def LogExample(value):
    for logger in debug.loggers:
        logger.log("Example:", value)
    if request:
        for value in request.values:
            if value > 0:
                LogExample(value) # deal with 'value' immediately
                break
```

### Moving Definitions Down

The original C programming language required all variable definitions to be at the top of the function or block. This requirement was unfortunate, because for long functions with many variables, it forced the reader to think about all those variables right away, even if they weren't used until much later. (C99 and C++ removed this requirement.)

In the following example, all the variables are innocently defined at the top of the function:

```python
def ViewFilteredReplies(original_id):
    filtered_replies = []
    root_message = Messages.objects.get(original_id)
    all_replies = Messages.objects.select(root_id=original_id)
    root_message.view_count += 1
    root_message.last_view_time = datetime.datetime.now()
    root_message.save()

    for reply in all_replies:
        if reply.spam_votes <= MAX_SPAM_VOTES:
            filtered_replies.append(reply)
    return filtered_replies
```

The problem with this example code is that it forces the reader to think about three variables at once, and switch gears between them.

Because the reader doesn’t need to know about all of them until later, it’s easy to just move each definition right before its first use:
```
def ViewFilteredReplies(original_id):
    root_message = Messages.objects.get(original_id)
    root_message.view_count += 1
    root_message.last_view_time = datetime.datetime.now()
    root_message.save()

    all_replies = Messages.objects.select(root_id=original_id)
    filtered_replies = []
    for reply in all_replies:
        if reply.spam_votes <= MAX_SPAM_VOTES:
            filtered_replies.append(reply)
    return filtered_replies

```

You might be wondering whether all_replies is a necessary variable, or if it could be eliminated by doing:
```
for reply in Messages.objects.select(root_id=original_id):
...
```
In this case, all_replies is a pretty good explaining variable, so we decided to keep it.

## Prefer Write-Once Variables


![](https://i.imgur.com/DsykYv5.png)

So far in this chapter, we’ve discussed how it’s harder to understand programs with lots of variables “in play.” Well, it’s even harder to think about variables that are constantly changing.
Keeping track of their values adds an extra degree of difficulty.

To combat this problem, we have a suggestion that may sound a little strange: prefer writeonce variables.

Variables that are a “permanent fixture” are easier to think about. Certainly, constants like:
```
static const int NUM_THREADS = 10;
```

don’t require much thought on the reader’s part. And for the same reason, use of const in C++ (and final in Java) is highly encouraged.

In fact, in many languages (including Python and Java), some built-in types like string are immutable. As James Gosling (Java’s creator) said, “[Immutables] tend to more often be trouble free.”

But even if you can’t make your variable write-once, it still helps if the variable changes in fewer places.

> KEY IDEA
> The more places a variable is manipulated, the harder it is to reason about its current value.

So how do you do it? How can you change a variable to be write-once? Well, a lot of times it requires restructuring the code a bit, as you’ll see in the next example.

## A Finael Example

For the final example of the chapter, we’d like to show an example demonstrating many of the principles we’ve discussed so far.
Suppose you have a web page with a number of input text fields, arranged like this:

```htmlembedded=
<input type="text" id="input1" value="Dustin">
<input type="text" id="input2" value="Trevor">
<input type="text" id="input3" value="">
<input type="text" id="input4" value="Melissa">
...
```

As you can see, the ids start with input1 and increment from there.

Your job is to write a function named setFirstEmptyInput() that takes a string and puts it in the first empty <input> on the page (in the example shown, "input3"). The function should return the DOM element that was updated (or null if there were no empty inputs left). Here is some code to do this that doesn’t apply the principles in this chapter:

```javascript=
var setFirstEmptyInput = function (new_value) {
    var found = false;
    var i = 1;
    var elem = document.getElementById('input' + i);
    while (elem !== null) {
        if (elem.value === '') {
            found = true;
            break;
        }
        i++;
        elem = document.getElementById('input' + i);
    }
    if (found) elem.value = new_value;
    return elem;
};
```
This code gets the job done, but it’s not pretty. What’s wrong with it, and how do we improve it?
There are a lot of ways to think about improving this code, but we’re going to consider it from the perspective of the variables it uses:
- var found
- var i
- var elem


All three of these variables exist for the entire function and are written to multiple times. Let’s try to improve the use of each of them.

As we discussed earlier in the chapter, intermediate variables like found can often be eliminated by returning early. Here’s that improvement:

```
var setFirstEmptyInput = function (new_value) {
    var i = 1;
    var elem = document.getElementById('input' + i);
    while (elem !== null) {
        if (elem.value === '') {
            elem.value = new_value;
            return elem;
        }
        i++;
        elem = document.getElementById('input' + i);
    }
    return null;
};
```
Next, take a look at elem. It’s used multiple times throughout the code in a very “loopy” way where it’s hard to keep track of its value. The code makes it seem as if elem is the value we’re iterating through, when really we’re just incrementing through i. So let’s restructure the while loop into a for loop over i:

```
var setFirstEmptyInput = function (new_value) {
    for (var i = 1; true; i++) {
        var elem = document.getElementById('input' + i);
        if (elem === null)
            return null; // Search Failed. No empty input found.
        if (elem.value === '') {
            elem.value = new_value;
            return elem;
        }
    }
};

```

In particular, notice how elem acts as a write-once variable whose lifespan is contained inside the loop. The use of true as a for loop condition is unusual, but in exchange, we are able to see the definition and modifications of i in a single line. (A traditional while (true) would also be reasonable.)

## Summary
This chapter is about how the variables in a program can quickly accumulate and become too much to keep track of. You can make your code easier to read by having fewer variables and making them as “lightweight” as possible. Specifically:
- Eliminate variables that just get in the way. In particular, we showed a few examples of how to eliminate “intermediate result” variables by handling the result immediately.
- Reduce the scope of each variable to be as small as possible. Move each variable to a place where the fewest lines of code can see it. Out of sight is out of mind.
- Prefer write-once variables. Variables that are set only once (or const, final, or otherwise immutable) make code easier to understand.



























