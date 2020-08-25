# CHAPTER TWELVE: Turning Thoughts into Code

![](https://i.imgur.com/Caz6hHb.png)

> You do not really understand something unless you can explain it to your grandmother.
> —Albert Einstein
> 

When explaining a complex idea to someone, it's easy to confuse them with all the little details. It's a valuable skill to be able to explain an idea “in plain English,” so that someone less knowledgeable than you can understand. It requires distilling an idea down to the most important concepts. Doing this not only helps the other person understand but also helps you think about your own ideas more clearly.

The same skill should be used when “presenting” code to your reader. We take the view that source code is the primary way to explain what a program is doing. So the code should be written “in plain English.”

In this chapter, we’ll use a simple process that can help you code more clearly:
    1. Describe what code needs to do, in plain English, as you would to a colleague.
    2. Pay attention to the key words and phrases used in this description.
    3. Write your code to match this description.

## Describing Logic Clearly

Here is a snippet of code from a web page in PHP. This code is at the top of a secured page. It checks whether the user is authorized to see the page, and if not, immediately returns a page telling the user she is not authorized:

```
$is_admin = is_admin_request();
if ($document) {
    if (!$is_admin && ($document['username'] != $_SESSION['username'])) {
        return not_authorized();
    }
} else {
    if (!$is_admin) {
        return not_authorized();
    }
}

// continue rendering the page ...
```

There’s quite a bit of logic in this code. As you saw in Part II, Simplifying Loops and Logic, large logic trees like this aren’t easy to understand. The logic in this code can probably be simplified, but how? Let’s start by describing the logic in plain English:

```
There are two ways you can be authorized:
1) you are an admin
2) you own the current document (if there is one)
Otherwise, you are not authorized.
```
Here is an alternative solution inspired by this description:

```
if (is_admin_request()) {
    // authorized
} elseif ($document && ($document['username'] == $_SESSION['username'])) {
    // authorized
} else {
    return not_authorized();
}

// continue rendering the page ...
```
This version is slightly unusual because it has two empty bodies. But the code is smaller, and the logic is simpler, because there is no negation. (The previous solution had three “nots.”) The bottom line is that it’s easier to understand.

## Knowing Your Libraries Helps
We once had a website that included a “tips box” that showed the user helpful suggestions like:

```
Tip: Log in to see your past queries. [Show me another tip!]
```

There were a few dozen tips, and all of them were hidden inside the HTML:

```htmlmixed=
<div id="tip-1" class="tip">Tip: Log in to see your past queries.</div>
<div id="tip-2" class="tip">Tip: Click on a picture to see it close up.</div>
...
```
When a user visited the page, one of these divs was randomly made visible, and the rest stayed hidden.

If the “Show me another tip!" link was clicked, it cycled to the next tip. Here is some code to implement that feature using the jQuery JavaScript library:

```javascript=
var show_next_tip = function () {
    var num_tips = $('.tip').size();
    var shown_tip = $('.tip:visible');
    
    var shown_tip_num = Number(shown_tip.attr('id').slice(4));
    if (shown_tip_num === num_tips) {
        $('#tip-1').show();
    } else {
        $('#tip-' + (shown_tip_num + 1)).show();
    }
    shown_tip.hide();
};
```

This code is okay. But it can be made better. Let’s start by describing, in words, what this code is trying to do:

```
Find the currently visible tip and hide it.
Then find the next tip after it and show that.
If we've run out of tips, cycle back to the first tip.
```
Based on this description, here’s another solution:

```javascript=
var show_next_tip = function () {
    var cur_tip = $('.tip:visible').hide(); // find the currently visible tip and hide it
    var next_tip = cur_tip.next('.tip'); // find the next tip after it
    if (next_tip.size() === 0) { // if we've run out of tips,
        next_tip = $('.tip:first'); // cycle back to the first tip
    }
    next_tip.show(); // show the new tip
};
```
This solution contains fewer lines of code and doesn’t have to manipulate integers directly. It’s more aligned with how a person would think about the code.

In this case, it helped that jQuery has a .next() method we could use. Part of writing succinct code is being aware of what your library has to offer.

## Applying This Method to Larger Problems

The previous examples have applied our process to small blocks of code. In the next example, we’ll apply it to a larger function. As you’ll see, this method can help you break down your code by helping you identify what pieces you can break away.

Imagine we have a system that records stock purchases. Each transaction has four pieces of data:
- time (a precise date and time of the purchase)
- ticker_symbol (e.g., GOOG)
- price (e.g., $600)
- number_of_shares (e.g., 100)

For some strange reason, the data is spread across three separate database tables, as illustrated here. In each database, the time is the unique primary key.

![](https://i.imgur.com/i09gYMe.png)

Now we need to write a program to join the three tables back together (as an SQL JOIN operation would do). This step should be easy because the rows are all sorted by time, but unfortunately some of the rows are missing. You want to find all the rows where all three times match up, and ignore any rows that can’t be lined up, as shown in the previous illustration.

Here is some Python code that finds all the matching rows:

```python

```
This example code works, but there’s a lot going on with how the loop skips over unmatched rows. Some warning flags might have gone off in your head: Could this miss any rows? Might it read past the end-of-stream for any of the iterators?

So how can we make it more readable?

```python=
def PrintStockTransactions():
    stock_iter = db_read("SELECT time, ticker_symbol FROM ...")
    price_iter = ...
    num_shares_iter = ...

    # Iterate through all the rows of the 3 tables in parallel.
    while stock_iter and price_iter and num_shares_iter:
        stock_time = stock_iter.time
        price_time = price_iter.time
        num_shares_time = num_shares_iter.time

        # If all 3 rows don't have the same time, skip over the oldest row
        # Note: the "<=" below can't just be "<" in case there are 2 tied-oldest.
        if stock_time != price_time or stock_time != num_shares_time:
            if stock_time <= price_time and stock_time <= num_shares_time:
                stock_iter.NextRow()
            elif price_time <= stock_time and price_time <= num_shares_time:
                price_iter.NextRow()
            elif num_shares_time <= stock_time and num_shares_time <= price_time:
                num_shares_iter.NextRow()
            else:
                assert False # impossible
            continue

        assert stock_time == price_time == num_shares_time

        # Print the aligned rows.
        print "@", stock_time,
        print stock_iter.ticker_symbol,
        print price_iter.price,
        print num_shares_iter.number_of_shares

        stock_iter.NextRow()
        price_iter.NextRow()
        num_shares_iter.NextRow()
```

### An English Description of the Solution

Once again, let’s step back and describe in plain English what we’re trying to do:
```
We are reading three row iterators in parallel.
Whenever the rows' times don't line up, advance the rows so they do line up.
Then print the aligned rows, and advance the rows again.
Keep doing this until there are no more matching rows left.
```
Looking back at the original code, the messiest part was the block dealing with “advance the rows so they do line up.” To present the code more clearly, we can extract all that messy logic into a new function named AdvanceToMatchingTime().

Here’s a new version of the code, making use of this new function:

```python=
def PrintStockTransactions():
    stock_iter = ...
    price_iter = ...
    num_shares_iter = ...

    while True:
        time = AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter)
        if time is None:
            return

        # Print the aligned rows.
        print "@", time,
        print stock_iter.ticker_symbol,
        print price_iter.price,
        print num_shares_iter.number_of_shares
        
        stock_iter.NextRow()
        price_iter.NextRow()
        num_shares_iter.NextRow()
```

As you can see, this code is much easier to understand, as we’ve hidden away all the dirty details of lining up the rows.

### Applying the Method Recursively

It’s easy to imagine how you would write AdvanceToMatchingTime()—in the worst case, it would look very similar to that ugly block of code from the first version:

```python=
def AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter):
    # Iterate through all the rows of the 3 tables in parallel.
    while stock_iter and price_iter and num_shares_iter:
        stock_time = stock_iter.time
        price_time = price_iter.time
        num_shares_time = num_shares_iter.time

        # If all 3 rows don't have the same time, skip over the oldest row
        if stock_time != price_time or stock_time != num_shares_time:
            if stock_time <= price_time and stock_time <= num_shares_time:
              stock_iter.NextRow()
            elif price_time <= stock_time and price_time <= num_shares_time:
                price_iter.NextRow()
            elif num_shares_time <= stock_time and num_shares_time <= price_time:
                num_shares_iter.NextRow()
            else:
                assert False # impossible
            continue
            assert stock_time == price_time == num_shares_time
            return stock_time
```

But let’s improve that code by applying our method to AdvanceToMatchingTime() as well. Here’s a description of what this function needs to do:

```
Look at the times of each current row: if they're aligned, we're done.
Otherwise, advance any rows that are "behind."
Keep doing this until the rows are aligned (or one of the iterators has ended).
```

This description is a lot clearer and more elegant than the previous code. One thing to notice is that the description never mentions stock_iter or other details specific to our problem. This means we can also rename the variables to be simpler and more general. Here’s the resulting code:

```python
def AdvanceToMatchingTime(row_iter1, row_iter2, row_iter3):
    while row_iter1 and row_iter2 and row_iter3:
        t1 = row_iter1.time
        t2 = row_iter2.time
        t3 = row_iter3.time

        if t1 == t2 == t3:
            return t1
        
        tmax = max(t1, t2, t3)
        
        # If any row is "behind," advance it.
        # Eventually, this while loop will align them all.
        if t1 < tmax: row_iter1.NextRow()
        if t2 < tmax: row_iter2.NextRow()
        if t3 < tmax: row_iter3.NextRow()
    return None # no alignment could be found
```

As you can see, this code is a lot clearer than before. The algorithm became simpler, and now there are fewer tricky comparisons. And we used short names like t1 and no longer had to think about the specific database columns involved.

## Summary

This chapter discussed the simple technique of describing your program in plain English and using that description to help you write more natural code. This technique is deceptively simple, but very powerful. Looking at the words and phrases used in your description can also help you identify which subproblems to break off.

But this process of “saying things in plain English” is applicable outside of just writing code. For example, one college computer lab policy states that when a student needs help debugging his program, he first has to explain the problem to a dedicated teddy bear in the corner of the room. Surprisingly, just describing the problem aloud can often help the student figure out a solution. This technique is called “rubber ducking.”

Another way to look at it is this: if you can't describe the problem or your design in words, something is probably missing or undefined. Getting a program (or any idea) into words can really force it into shape.



















