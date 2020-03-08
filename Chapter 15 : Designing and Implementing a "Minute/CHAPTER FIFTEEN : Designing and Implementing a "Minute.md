# CHAPTER FIFTEEN : Designing and Implementing a "Minute/Hour Counter"

![](https://i.imgur.com/5mkouaz.png)

Let’s take a look at a data structure used in real production code: a “minute/hour counter.” We’ll take you through the natural thought process an engineer might go through, first trying to solve this problem and then improving its performance and adding features. Most important, we’ll also be trying to keep the code easy to read, using principles from throughout this book. We might take some wrong turns along the way or make other mistakes. See if you can follow along and catch them

## The Problem

We need to keep track of how many bytes a web server has transferred over the past minute and over the past hour. Here’s an illustration of how these totals are maintained:

![](https://i.imgur.com/g7VrkSb.png)

It’s a fairly straightforward problem, but as you’ll see, solving it efficiently is an interesting challenge. Let’s start by defining the class interface.

## Defining the Class Interface

Here is our first version of the class interface in C++:

```
class MinuteHourCounter {
    public:
        // Add a count
        void Count(int num_bytes);
        
        // Return the count over this minute
        int MinuteCount();
        
        // Return the count over this hour
        int HourCount();
};
```

Before we implement this class, let’s go through the names and comments to see if there’s anything we want to change

### Improving the Names

The class name MinuteHourCounter is pretty good. It’s very specific, concrete, and easy to say.

Given the class name, the method names MinuteCount() and HourCount() are also reasonable. You might have called them GetMinuteCount() and GetHourCount(), but this doesn’t help anything. As we said in Chapter 3, Names That Can’t Be Misconstrued, “get” implies “lightweight accessor” to many people. And as you’ll see, the implementation won’t be lightweight, so it’s best to leave “get” out.

The method name Count() is problematic, though. We asked our coworkers what they thought Count() would do, and some thought it meant “return the total number of counts over all time.” The name is a bit counterintuitive (no pun intended). The problem is that Count is both a noun and a verb and could mean either “I want a count of the number of samples you have seen” or “I want you to count this sample.”

Here are alternative names to consider in place of Count():
- Increment()
- Observe()
- Record()
- Add()

Increment() is misleading because it implies that there’s a value that only increases. (In our case, the hour count fluctuates over time.)

Observe() is okay, but a little vague.

Record() also has the noun/verb problem, so that’s no good.

Add() is interesting because it can either mean “add this numerically” or “add to a list of data”— in our case, it’s a little of both, so that works. So we’ll rename the method to void Add(int num_bytes).

But the argument name num_bytes is too specific. Yes, our primary use case is for counting bytes, but MinuteHourCounter doesn’t need to know this. Someone else might use this class to count queries or database transactions. We could use a more generic name like delta, but the term delta is often used in places where the value can be negative, which we don’t want. The name count should work—it’s simple, generic, and implies “nonnegative.” Also, it lets us sneak in the word “count” in a less ambiguous context.

### Improving the Comments

Here’s the class interface we have so far:

```
class MinuteHourCounter {
    public:
    // Add a count
    void Add(int count);

    // Return the count over this minute
    int MinuteCount();
    
    // Return the count over this hour
    int HourCount();
};
```
Let’s go through each of these method comments and improve them. Consider the first one:

```
// Add a count
void Add(int count);
```

This comment is completely redundant now—it should be either removed or improved. Here’s an improved version:

```
// Add a new data point (count >= 0).
// For the next minute, MinuteCount() will be larger by +count.
// For the next hour, HourCount() will be larger by +count.
void Add(int count);
```

Now let’s consider the comment for MinuteCount():

```
// Return the count over this minute
int MinuteCount();
```
When we asked our coworkers what this comment meant, there were two conflicting interpretations:

1. Return the count during this current clock-minute, such as 12:13 p.m.
2. Return the count during the past 60 seconds, regardless of clock-minute boundaries.

The second interpretation is how it actually works. So let’s clear up this confusion with language that is more precise and detailed:

```
// Return the accumulated count over the past 60 seconds.
int MinuteCount();
```

(Similarly, we should improve the comment for HourCount().)

Here is the class definition with all the changes so far, along with a class-level comment:

```
// Track the cumulative counts over the past minute and over the past hour.
// Useful, for example, to track recent bandwidth usage.
class MinuteHourCounter {
    // Add a new data point (count >= 0).
    // For the next minute, MinuteCount() will be larger by +count.
    // For the next hour, HourCount() will be larger by +count.
    void Add(int count);
    
    // Return the accumulated count over the past 60 seconds.
    int MinuteCount();
    // Return the accumulated count over the past 3600 seconds.
    int HourCount();
};
```
(For brevity, we’ll leave the comments out of the code listings from now on.)

> GETTING AN OUTSIDE PERSPECTIVE
> You may have noticed that there were already a couple cases where we ran things by our coworkers. Asking for an outside perspective is a great way to test if your code is “user-friendly.” Try to be open to their first impressions, because other people may come to the same conclusions. And those “other people” may include you in six months

## Attempt 1: A Naive Solution

Let’s move on to solving the problem. We’ll start with a straightforward solution: just keep a list of timestamped “events”:

```
class MinuteHourCounter {
    struct Event {
        Event(int count, time_t time) : count(count), time(time) {}
        int count;
        time_t time;
    };

    list<Event> events;

    public:
        void Add(int count) {
            events.push_back(Event(count, time()));
        }
    ...
};
```
We can then count over the most recent events as needed:
```
class MinuteHourCounter {
    ...
    int MinuteCount() {
        int count = 0;
        const time_t now_secs = time();
        for (list<Event>::reverse_iterator i = events.rbegin();
            i != events.rend() && i->time > now_secs - 60; ++i) {
            count += i->count;
        }
        return count;
    }

    int HourCount() {
        int count = 0;
        const time_t now_secs = time();
        for (list<Event>::reverse_iterator i = events.rbegin();
            i != events.rend() && i->time > now_secs - 3600; ++i) {
            count += i->count;
        }
        return count;
    }
};
```
### Is the Code Easy to Understand?
Although this solution is “correct,” there are a couple readability problems:
- The for loops are a bit of a mouthful. Most readers slow down significantly while they’re reading this part of the code (at least they should, if they’re making sure there aren’t any bugs).
- MinuteCount() and HourCount() are almost identical. It would make the code smaller if they could share the duplicated code. This detail is especially important because the redundant code is relatively complex. (Better to have all the difficult code confined to one place.)

### An Easier-to-Read Version
The code for MinuteCount() and HourCount() differs by only a single constant (60 vs. 3600). The obvious refactoring is to introduce a helper method to handle both cases:

```
class MinuteHourCounter {
    list<Event> events;

    int CountSince(time_t cutoff) {
        int count = 0;
        for (list<Event>::reverse_iterator rit = events.rbegin();
            rit != events.rend(); ++rit) {
            if (rit->time <= cutoff) {
                break;
            }
            count += rit->count;
        }
        return count;
    }

    public:
        void Add(int count) {
            events.push_back(Event(count, time()));
        }

    int MinuteCount() {
        return CountSince(time() - 60);
    }

    int HourCount() {
        return CountSince(time() - 3600);
    }
};

```
There are a few things worth pointing out about this new code.

First, notice that CountSince() takes an absolute cutoff parameter, rather than a relative secs_ago value (60 or 3600). Either way would have worked, but this way CountSince() has a slightly easier job to do.

Second, we renamed the iterator from i to rit. The name i is more commonly used for integer indexes. We contemplated using the name it, which is typical for iterators. But in this case we have a reverse iterator, and this fact is crucial to the correctness of the code. By having a variable name prefixed with r, it adds a comforting symmetry to statements like rit != events.rend().

Finally, we extracted the condition rit->time <= cutoff out of the for loop, and made it a separate if statement. Why? Because “traditional” for loops of the form for(begin; end; advance) are easiest to read. The reader can immediately understand it as “go through all the elements” and doesn’t have to think about it further.

### Performance Problems

Although we’ve improved how the code looks, this design has two serious performance problems:
    1. It just keeps growing and growing.
        The class holds on to all of the events it’s ever seen—it uses an unbounded amount of memory! Ideally, the MinuteHourCounter should automatically delete events that are older than an hour because they’re no longer needed.
    2. MinuteCount() and HourCount() are too slow.
        The method CountSince() takes O(n) time, where n is the number of data points in the relevant time window. Imagine a high-performance server that called Add() hundreds of times per second. Every call to HourCount() would have to count through a million data points! Ideally, the MinuteHourCounter should keep separate minute_count and hour_count variables that are kept up date with each call to Add()

## Attempt 2: Conveyor Belt Design

We need a design that solves both of the previous problems

1. Delete data we no longer need.
2. Keep precomputed minute_count and hour_count totals up to date.

Here’s how we’ll do it: we’ll use our list like a conveyor belt. When new data arrives on one end, we add to our total. And when the data is too old, it “falls off” the other end, and we subtract from our total.

There are a couple ways we could implement this conveyor belt design. One way is to maintain two independent lists, one for events in the past minute, one for those in the past hour. When a new event comes in, add a copy to both lists.

![](https://i.imgur.com/tHh7T1R.png)

This way is pretty simple, but it’s inefficient because it makes two copies of every event.

Another way is to maintain two lists, where events initially go into the first list (the “last minute events”), and then this feeds into the second list (the “last hour [but not last minute] events”).

![](https://i.imgur.com/6O5rzjV.png)

This “two-stage” conveyor belt design seems more efficient, so let’s implement this one.

### Implementing the Two-Stage Conveyor Belt Design

Let’s begin by listing the members of our class:

```
class MinuteHourCounter {
    list<Event> minute_events;
    list<Event> hour_events; // only contains elements NOT in minute_events
    int minute_count;
    int hour_count; // counts ALL events over past hour, including past minute
};
```
The crux of this conveyor belt design is to be able to “shift” the events as time goes by, so that events move from minute_events to hour_events, and minute_count and hour_count get updated accordingly. To do this, we’ll create a helper method named ShiftOldEvents(). Once we have that method, the rest of the class is fairly easy to implement:

```
void Add(int count) {
    const time_t now_secs = time();
    ShiftOldEvents(now_secs);

    // Feed into the minute list (not into the hour list--that will happen later)
    minute_events.push_back(Event(count, now_secs));
    
    minute_count += count;
    hour_count += count;
}

int MinuteCount() {
    ShiftOldEvents(time());
    return minute_count;
}

int HourCount() {
    ShiftOldEvents(time());
    return hour_count;
}
```

Clearly, we’ve deferred all the dirty work to ShiftOldEvents():

```
// Find and delete old events, and decrease hour_count and minute_count accordingly.
void ShiftOldEvents(time_t now_secs) {
    const int minute_ago = now_secs - 60;
    const int hour_ago = now_secs - 3600;

    // Move events more than one minute old from 'minute_events' into 'hour_events'
    // (Events older than one hour will be removed in the second loop.)
    while (!minute_events.empty() && minute_events.front().time <= minute_ago) {
        hour_events.push_back(minute_events.front());
        minute_count -= minute_events.front().count;
        minute_events.pop_front();
    }

    // Remove events more than one hour old from 'hour_events'
    while (!hour_events.empty() && hour_events.front().time <= hour_ago) {
        hour_count -= hour_events.front().count;
        hour_events.pop_front();
    }
}
```

### Are We Done?

We’ve solved the two performance concerns we mentioned earlier, and our solution works. For many applications, this solution would be good enough. But there are a number of deficiencies, too.

First, the design is very inflexible. Suppose we wanted to keep counts over the past 24 hours. That would require making a lot of changes to the code. And as you probably noticed, ShiftOldEvents() is a pretty dense function, with subtle interaction between the minute and hour data.

Second, this class has a pretty big memory footprint. Suppose you had a high-traffic server calling Add() 100 times per second. Because we hold on to all data over the past hour, this code would end up requiring about 5MB of memory.

In general, the more frequently Add() is called, the more memory we use. In a production environment, libraries that use a large, unpredictable amount of memory aren’t good. Ideally, the MinuteHourCounter would use a fixed amount of memory no matter how often Add() is called

## Attempt 3: A Time-Bucketed Design

You may not have noticed, but both of the previous implementations had a small bug. We used time_t to store the timestamp, which stores an integral number of seconds. Because of this rounding, MinuteCount() actually returns somewhere between 59 and 60 seconds worth of data, depending on when exactly you call it.

For example, if an event happens at time = 0.99 seconds, that time will get rounded to t=0 seconds. And if you call MinuteCount() at time = 60.1 seconds, it will return the total for events where t=1,2,3,...60. So that first event will be missed, even though it’s technically less than a minute ago.

On average, MinuteCount() will return 59.5 seconds worth of data. And HourCount() will return 3599.5 seconds worth of data (a negligible error).

We could fix all this by using a time with subsecond granularity. But interestingly, most applications using a MinuteHourCounter don’t need that level of accuracy in the first place. We will exploit this fact to design a new MinuteHourCounter that’s much faster and uses less space. It’s a trade-off of precision for performance that will be well worth it.

The key idea is to bucket all the events within a small time window together, and summarize those events with a single total. For instance, the events over the past minute could be inserted into 60 discrete buckets, each 1 second wide. The events over the past hour could also be inserted into 60 discrete buckets, each 1 minute wide.

![](https://i.imgur.com/RF7nsZG.png)

Using the buckets as shown, the methods MinuteCount() and HourCount() will be accurate to 1 part per 60, which is reasonable.*

If more precision is needed, more buckets can be used in exchange for a larger memory footprint. But the important thing is that this design has a fixed, predictable memory usage.

### Implementing the Time-Bucketed Design

Implementing this design with just one class would create a lot of intricate code that’s hard to wrap your head around. Instead, we’re going to follow our advice from Chapter 11, One Task at a Time, and create separate classes to handle the different parts of this problem.

For starters, let’s create a separate class to keep track of the counts for a single time span (like the last hour). We’ll call it a TrailingBucketCounter. It’s essentially a generic version of MinuteHourCounter that handles only one time span. Here’s the interface:

```
// A class that keeps counts for the past N buckets of time.
class TrailingBucketCounter {
    public:
        // Example: TrailingBucketCounter(30, 60) tracks the last 30 minute-buckets of time.
        TrailingBucketCounter(int num_buckets, int secs_per_bucket);
    
        void Add(int count, time_t now);
    
        // Return the total count over the last num_buckets worth of time
        int TrailingCount(time_t now);
};
```
You might wonder why Add() and TrailingCount() require the current time (time_t now) as an argument—wouldn’t it be easier if those methods just computed the current time() themselves?

>Similar to the previous solutions, the last bucket will be only half-full on average. With this design, we could remedy the underestimate by keeping 61 buckets instead of 60 and ignoring the current “inprogress” bucket. But this causes the data to be partially “stale.” A better fix is to combine the in-progress bucket with a complementary fraction of the oldest bucket to obtain a count that is both unbiased and up to date. This implementation is left as an exercise for the reader.

Although it may seem strange, passing in the current time has a couple benefits. First, it makes TrailingBucketCounter a “clockless” class, which in general is easier to test and less bug-prone. Second, it keeps all the calls to time() inside MinuteHourCounter. With time-sensitive systems, it helps if you can put all the calls to get the time in one place.

Assuming TrailingBucketCounter was already implemented, the MinuteHourCounter is easy to implement:

```
class MinuteHourCounter {
    TrailingBucketCounter minute_counts;
    TrailingBucketCounter hour_counts;
    
    public:
        MinuteHourCounter() :
            minute_counts(/* num_buckets = */ 60, /* secs_per_bucket = */ 1),
            hour_counts( /* num_buckets = */ 60, /* secs_per_bucket = */ 60) {
    }
    
    void Add(int count) {
        time_t now = time();
        minute_counts.Add(count, now);
        hour_counts.Add(count, now);
    }

    int MinuteCount() {
        time_t now = time();
        return minute_counts.TrailingCount(now);
    }

    int HourCount() {
        time_t now = time();
        return hour_counts.TrailingCount(now);
    }
};
```

This code is much more readable, and also more flexible—if we wanted to increase the number of buckets (to improve precision but increase memory usage), that would be easy to do.

### Implementing TrailingBucketCounter

Now all that’s left is to implement the TrailingBucketCounter class. Once again, we’re going to create a helper class to break down this problem further.

We’ll create a data structure called ConveyorQueue whose job is to deal with the underlying counts and their totals. The TrailingBucketCounter class can focus on the task of moving the ConveyorQueue according to how much time has elapsed.

Here is the ConveyorQueue interface:

```
// A queue with a maximum number of slots, where old data "falls off" the end.
class ConveyorQueue {
    ConveyorQueue(int max_items);
    
    // Increment the value at the back of the queue.
    void AddToBack(int count);

    // Each value in the queue is shifted forward by 'num_shifted'.
    // New items are initialized to 0.
    // Oldest items will be removed so there are <= max_items.
    void Shift(int num_shifted);
    
    // Return the total value of all items currently in the queue.
    int TotalSum();
};
```
Assuming this class was implemented, look how easy the TrailingBucketCounter is to implement:

```python=
class TrailingBucketCounter {
    ConveyorQueue buckets;
    const int secs_per_bucket;
    time_t last_update_time; // the last time Update() was called
    
    // Calculate how many buckets of time have passed and Shift() accordingly.
    void Update(time_t now) {
        int current_bucket = now / secs_per_bucket;
        int last_update_bucket = last_update_time / secs_per_bucket;
        
        buckets.Shift(current_bucket - last_update_bucket);
        last_update_time = now;
    }

    public:
        TrailingBucketCounter(int num_buckets, int secs_per_bucket) :
            buckets(num_buckets),
            secs_per_bucket(secs_per_bucket) {
        }

        void Add(int count, time_t now) {
            Update(now);
            buckets.AddToBack(count);
        }

        int TrailingCount(time_t now) {
            Update(now);
            return buckets.TotalSum();
        }
};
```

This breakdown into two classes (TrailingBucketCounter and ConveyorQueue) is another instance of what we discussed in Chapter 11, One Task at a Time. We could also have done without ConveyorQueue and implemented everything directly inside TrailingBucketCounter. But this way, the code is easier to digest.

### Implementing ConveyorQueue

Now all that’s left is to implement the ConveyorQueue class:

```
// A queue with a maximum number of slots, where old data gets shifted off the end.
class ConveyorQueue {
    queue<int> q;
    int max_items;
    int total_sum; // sum of all items in q
    
    public:
        ConveyorQueue(int max_items) : max_items(max_items), total_sum(0) {
        }

        int TotalSum() {
            return total_sum;
        }

        void Shift(int num_shifted) {
            // In case too many items shifted, just clear the queue.
            if (num_shifted >= max_items) {
                q = queue<int>(); // clear the queue
                total_sum = 0;
                return;
        }

            // Push all the needed zeros.
            while (num_shifted > 0) {
                q.push(0);
                num_shifted--;
            }

            // Let all the excess items fall off.
            while (q.size() > max_items) {
                total_sum -= q.front();
                q.pop();
                }
        }

        void AddToBack(int count) {
            if (q.empty()) Shift(1); // Make sure q has at least 1 item.
            q.back() += count;
            total_sum += count;
        }
};

```
Now we’re done! We have a MinuteHourCounter that’s fast and memory-efficient, plus a more flexible TrailingBucketCounter that’s easily reusable. For instance, it would be pretty easy to create a more versatile RecentCounter that can count a wide range of intervals, such as the last day or last ten minutes

## Comparing the Three Solutions

Let’s compare the solutions we’ve looked at in this chapter. The following table shows the code size and performance stats (assuming a high-traffic use case of 100 Add()/sec):



| Solution | Lines of code | Cost per HourCount()|Memory use |Error in HourCount()|
| -------- | -------- | -------- |-------- | -------- |
| Naive solution     | 33     | O(#events-per-hour)(~3.6 million)     | unbounded     | 1 part per 3600     |
| Conveyor belt design      | 55     | O(1)     | O(#events-per-hour)(~5MB)     | 1 part per 3600     |
| Time-bucketed design (60 butkets)     | 98     | O(1)     | O(#buckets)(~500 bytes)     | 1 part per 60     |

Notice that the total amount of code for our final three-class solution is more than for any of the other attempts. However, the performance is far superior, and the design is more flexible. Also, each class individually is much easier to read. This is always a positive change: having 100 lines that are all easy to read is better than 50 lines that aren’t.

Sometimes, breaking a problem into multiple classes can introduce interclass complexity (that a one-class solution wouldn’t have). In this case, though, there’s a simple “linear” chain of use from one class to the next, and only one of the classes is exposed to end users. Overall, the benefits of breaking this problem down make this a win.

## Summary

Let’s review the steps we went through to get to the final MinuteHourCounter design. The process is typical of how other pieces of code evolve.

First, we started by coding a naive solution. This helped us realize two design challenges: speed and memory use.

Next, we tried a “conveyor belt” design. This design improved the speed and memory use but still wasn’t good enough for high-performance applications. Also, this design was very inflexible: adapting the code to handle other time intervals would be a lot of work

Our final design solved the previous problems by breaking things down into subproblems. Here are the three classes we created, in bottom-up order, and the subproblem each one solved:

ConveyorQueue
    A maximum-length queue that can be “shifted” and maintains its total sum
    
TrailingBucketCounter
    Moves the ConveyorQueue according to how much time has elapsed and maintains the count of a single (latest) time interval, with a given precision

MinuteHourCounter
    Simply contains two TrailingBucketCounters, one for the minute count and one for the hour count

