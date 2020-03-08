# CHAPTER ELEVENT: One Task at a Time

![](https://i.imgur.com/nmnce4t.png)
Code that does multiple things at once is harder to understand. A single block of code might be initializing new objects, cleansing data, parsing inputs, and applying business logic, all at the same time. If all that code is woven together, it will be harder to understand than if each “task” is started and completed on its own.

> KEY IDEA
> Code should be organized so that it’s doing only one task at a time.
> 

Said another way, this chapter is about “defragmenting” your code. The following diagram illustrates this process: the left side shows the various tasks a piece of code is doing, and the right side shows that same code after it’s been organized to do one task at a time.

![](https://i.imgur.com/a6YkYf8.png)
You might have heard the advice that “functions should do only one thing.” Our advice is similar, but isn’t always about function boundaries. Sure, breaking a large function into multiple smaller functions can be good. But even if you don’t do this, you can still organize the code inside that large function so it feels like there are separate logical sections.

Here’s the process we use to make code do “one task at a time”:
    1. List out all the “tasks” your code is doing. We use the word “task” very loosely—it could be as small as “make sure this object is valid” or as vague as “iterate through every node in the tree.”
    2. Try to separate those tasks as much as you can into different functions or at least different sections of code.

In this chapter, we’ll show you a number of examples of how to do this.

## Tasks Can Be Small

Suppose there’s a voting widget on a blog where a user can vote a comment “Up” or “Down.” The total score of a comment is the sum over all votes: +1 for each “Up” vote, –1 for each “Down” vote.

Here are the three states a user’s vote can be in, and how it would affect the total score:

![](https://i.imgur.com/VmQycYa.png)

When the user clicks one of the buttons (to make/change her vote), the following JavaScript is called:

```
vote_changed(old_vote, new_vote); // each vote is "Up", "Down", or ""
```
This function updates the total score and works for all combinations of old_vote/new_vote:
```javascript=
var vote_changed = function (old_vote, new_vote) {
    var score = get_score();

    if (new_vote !== old_vote) {
        if (new_vote === 'Up') {
            score += (old_vote === 'Down' ? 2 : 1);
        } else if (new_vote === 'Down') {
            score -= (old_vote === 'Up' ? 2 : 1);
        } else if (new_vote === '') {
            score += (old_vote === 'Up' ? -1 : 1);
        }
    }
    set_score(score);
};
```
Even though the code is pretty short, it’s doing a lot. There are lots of intricate details, and it’s hard to tell at a glance whether there are any off-by-one errors, typos, or other bugs.

The code may seem to be doing only one thing (updating the score), but there are actually two tasks being performed at once:
    1. old_vote and new_vote are being “parsed” into numerical values.
    2. score is being updated.

We can make the code easier to read by solving each task separately. The following code solves the first task, of parsing the vote into a numerical value:

```javascript=
var vote_value = function (vote) {
    if (vote === 'Up') {
        return +1;
    }

    if (vote === 'Down') {
        return -1;
    }
    return 0;
};
```
Now the rest of the code can solve the second task, updating score:
```javascript=
var vote_changed = function (old_vote, new_vote) {
    var score = get_score();
    score -= vote_value(old_vote); // remove the old vote
    score += vote_value(new_vote); // add the new vote
    set_score(score);
};
```
As you can see, this version of the code takes a lot less mental effort to convince yourself that it works. That’s a big part of what makes code “easy to understand.

## Extracting Values from an Object

We once had some JavaScript that formatted a user’s location into a friendly string of “City, Country” like “Santa Monica, USA” or “Paris, France.” We were given a location_info dictionary with plenty of structured information. All we had to do was pick a “City” and a “Country” from all the fields and concatenate them together.

The following illustration shows example input/output:

![](https://i.imgur.com/2TQ2i7Q.png)

It seems easy so far, but the tricky part is that any or all of these four values might be missing. Here’s how we dealt with that:

- When choosing the “City,” we preferred to use the “LocalityName” (city/town) if available, then the “SubAdministrativeAreaName” (larger city/county), then the “AdministrativeAreaName” (state/territory)
- If all three were missing, the “City” was affectionately given the default “Middle-ofNowhere.”
- If the “CountryName” was missing, “Planet Earth” was used as a default.

The following figure shows two examples of handling missing values.

![](https://i.imgur.com/cxoyuUm.png)

Here is the code we wrote to implement this task:
```javascript=
var place = location_info["LocalityName"]; // e.g. "Santa Monica"
if (!place) {
    place = location_info["SubAdministrativeAreaName"]; // e.g. "Los Angeles"
}
if (!place) {
    place = location_info["AdministrativeAreaName"]; // e.g. "California"
}
if (!place) {
    place = "Middle-of-Nowhere";
}
if (location_info["CountryName"]) {
    place += ", " + location_info["CountryName"]; // e.g. "USA"
} else {
    place += ", Planet Earth";
}

return place;
```

Sure, it's a little messy, but it got the job done.

But a few days later, we needed to improve the functionality: for locations in the United States, we wanted to display the state instead of the country (if possible). So instead of “Santa Monica, USA” it would return “Santa Monica, California.”

Adding this feature to the previous code would have made it much uglier.

### Applying “One Task at a Time”

Rather than bend this code to our will, we stopped and realized that it was already doing multiple tasks at the same time:

1. Extracting values from the dictionary location_info
2. Going through a preference order for “City,” defaulting to “Middle-of-Nowhere” if it couldn’t find anything
3. Getting the “Country,” and using “Planet Earth” if there wasn’t one
4. Updating place

So instead, we rewrote the original code to solve each of these tasks independently

The first task (extracting values from location_info) was easy to solve on its own:

```
var town = location_info["LocalityName"]; // e.g. "Santa Monica"
var city = location_info["SubAdministrativeAreaName"]; // e.g. "Los Angeles"
var state = location_info["AdministrativeAreaName"]; // e.g. "CA"
var country = location_info["CountryName"]; // e.g. "USA
```
At this point, we were done using location_info and didn’t have to remember those long and unintuitive keys. Instead, we had four simple variables to work with.

Next, we had to figure out what the “second half” of the return value would be:

```javascript=
// Start with the default, and keep overwriting with the most specific value.
var second_half = "Planet Earth";
if (country) {
    second_half = country;
}
if (state && country === "USA") {
    second_half = state;
}
```
Similarly, we could figure out the “first half”:
```javascript=
var first_half = "Middle-of-Nowhere";
if (state && country !== "USA") {
    first_half = state;
}
if (city) {
    first_half = city;
}
if (town) {
    first_half = town;
}
```
Finally, we pieced the information together:
```javascript=
return first_half + ", " + second_half;
```

The “defragmentation” illustration at the beginning of this chapter was actually a representation of the original solution and this new version. Here’s that same illustration, with more details filled in:

![](https://i.imgur.com/1wnoHYM.png)

As you can see, the four tasks in the second solution have been defragmented into distinct regions.

### Another Approach

When refactoring code, there are often multiple ways to do it, and this case is no exception. Once you’ve separated some of the tasks, the code gets easier to think about, and you might come up with even better ways to refactor it.

For instance, that earlier series of if statements requires some careful reading to know if every case works correctly. There are actually two subtasks going on simultaneously in that code:

    1. Go through a list of variables, and pick the most preferred one that’s available.
    2. Use a different list, depending on whether the country is “USA”.

Looking back, you can see that the earlier code has the “if USA” logic interwoven with the rest of the logic. Instead, we can handle the USA and non-USA cases separately:
```javascript=
var first_half, second_half;

if (country === "USA") {
    first_half = town || city || "Middle-of-Nowhere";
    second_half = state || "USA";
} else {
    first_half = town || city || state || "Middle-of-Nowhere";
    second_half = country || "Planet Earth";
}

return first_half + ", " + second_half;
```
In case you aren’t familiar with JavaScript, a || b || c is idiomatic and evaluates to the first “truthy” value (in this case, a defined, nonempty string). This code has the benefit that it’s very easy to inspect the preference list and update it. Most of the if statements have been swept away, and the business logic is represented by fewer lines of code.

## A Larger Example

In a web-crawling system we built, a function named UpdateCounts() was called to increment various statistics after each web page was downloaded

```
void UpdateCounts(HttpDownload hd) {
    counts["Exit State" ][hd.exit_state()]++; // e.g. "SUCCESS" or "FAILURE"
    counts["Http Response"][hd.http_response()]++; // e.g. "404 NOT FOUND"
    counts["Content-Type" ][hd.content_type()]++; // e.g. "text/html"
}
```
Well, that’s how we wish the code looked!

In actuality, the HttpDownload object had none of the methods shown here. Instead, HttpDownload was a very large and complex class, with many nested classes, and we had to fish out those values ourselves. To make matters worse, sometimes those values were missing altogether—in which case we just used "unknown" as the default value.

Because of all this, the real code was quite a mess:

```
// WARNING: DO NOT STARE DIRECTLY AT THIS CODE FOR EXTENDED PERIODS OF TIME.
void UpdateCounts(HttpDownload hd) {
    // Figure out the Exit State, if available.
    if (!hd.has_event_log() || !hd.event_log().has_exit_state()) {
        counts["Exit State"]["unknown"]++;
    } else {
        string state_str = ExitStateTypeName(hd.event_log().exit_state());
        counts["Exit State"][state_str]++;
    }

    // If there are no HTTP headers at all, use "unknown" for the remaining elements.
    if (!hd.has_http_headers()) {
        counts["Http Response"]["unknown"]++;
        counts["Content-Type"]["unknown"]++;
        return;
    }

    HttpHeaders headers = hd.http_headers();

    // Log the HTTP response, if known, otherwise log "unknown"
    if (!headers.has_response_code()) {
    c   ounts["Http Response"]["unknown"]++;
    } else {
        string code = StringPrintf("%d", headers.response_code());
        counts["Http Response"][code]++;
    }

    // Log the Content-Type if known, otherwise log "unknown"
    if (!headers.has_content_type()) {
        counts["Content-Type"]["unknown"]++;
    } else {
        string content_type = ContentTypeMime(headers.content_type());
        counts["Content-Type"][content_type]++;
    }
}
```

As you can see, there’s a lot of code, and a lot of logic, and even a few repeated lines of code. This code is not fun to read.

In particular, this code switches back and forth between different tasks. Here are the different tasks interleaved throughout the code:

1. Using "unknown" as the default value for each key
2. Detecting whether members of HttpDownload are missing
3. Extracting the value and converting it to a string
4. Updating counts[]

We can improve the code by separating some of these tasks into distinct regions in the code:

```
void UpdateCounts(HttpDownload hd) {
    // Task: define default values for each of the values we want to extract
    string exit_state = "unknown";
    string http_response = "unknown";
    string content_type = "unknown";

    // Task: try to extract each value from HttpDownload, one by one
    if (hd.has_event_log() && hd.event_log().has_exit_state()) {
        exit_state = ExitStateTypeName(hd.event_log().exit_state());
    }
    if (hd.has_http_headers() && hd.http_headers().has_response_code()) {
        http_response = StringPrintf("%d", hd.http_headers().response_code());
    }
    if (hd.has_http_headers() && hd.http_headers().has_content_type()) {
        content_type = ContentTypeMime(hd.http_headers().content_type());
    }
    
    // Task: update counts[]
    counts["Exit State"][exit_state]++;
    counts["Http Response"][http_response]++;
    counts["Content-Type"][content_type]++;
}
```
As you can see, the code has three separate regions with the following aims:
1. Define defaults for the three keys we are interested in.
2. Extract the values, if available, for each of these keys, and convert them to strings.
3. Update counts[] for each key/value.

What’s good about these regions is that they’re isolated from one another—while you’re reading one region, you don’t need to think about the other regions.

Note that although we listed four tasks, we were able to separate only three of them. That’s perfectly fine: the tasks you list initially are just a starting point. Even separating some of them can help things a lot, as it did here.

### Further Improvements

This new version of the code is a marked improvement from the original monstrosity. And notice that we didn’t even have to create other functions to perform this cleanup. As we mentioned before, the idea of “one task at a time” can help you clean up code regardless of function boundaries.

However, we could also have improved this code another way, by introducing three helper functions:

```
void UpdateCounts(HttpDownload hd) {
    counts["Exit State"][ExitState(hd)]++;
    counts["Http Response"][HttpResponse(hd)]++;
    counts["Content-Type"][ContentType(hd)]++;
}
```
These functions would extract the corresponding value, or return “unknown”. For example:
```
string ExitState(HttpDownload hd) {
    if (hd.has_event_log() && hd.event_log().has_exit_state()) {
        return ExitStateTypeName(hd.event_log().exit_state());
    } else {
        return "unknown";
    }
}
```
Notice that this alternative solution doesn’t even define any variables! As we mentioned in Chapter 9, Variables and Readability, variables that hold intermediate results can often be eliminated entirely.

In this solution, we’ve simply “sliced” the problem in a different direction. Both solutions are highly readable, as they require the reader to think about only one task at a time.

## Summary
This chapter illustrates a simple technique for organizing your code: do only one task at a time.

If you have code that’s difficult to read, try to list all of the tasks it’s doing. Some of these tasks might easily become separate functions (or classes). Others might just become logical “paragraphs” within a single function. The exact details of how you separate these tasks isn’t as important as the fact that they’re separated. The hard part is accurately describing all the little things your program is doing
















