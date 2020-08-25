# CHAPTER THIRTEEN: Writing Less Code

![](https://i.imgur.com/J9QClkQ.png)

Knowing when not to code is possibly the most important skill a programmer can learn. Every line of code you write is a line that has to be tested and maintained. By reusing libraries or eliminating features, you can save time and keep your codebase lean and mean.

> KEY IDEA
> The most readable code is no code at all.
> 
## Don’t Bother Implementing That Feature—You Won’t Need It
When you start a project, it’s natural to get excited and think of all the cool features you’ll want to implement. But programmers tend to overestimate how many features are truly essential to their project. A lot of features go unfinished or unused or just complicate the application.

Programmers also tend to underestimate how much effort it takes to implement a feature. We optimistically estimate how long it will take to implement a crude prototype but forget how much extra time is involved in future maintenance, documentation, and the added “weight”
to the codebase.

## Question and Break Down Your Requirements
Not all programs need to be fast, 100% correct, and able to handle every possible input. If you really scrutinize your requirements, sometimes you can carve out a simpler problem that requires less code. Let’s look at some examples of this.

### Example: A Store Locator

Suppose you were writing a “store locator” for a business. You think your requirements are:
    For any given user’s latitude/longitude, find the store with the closest latitude/longitude.
    
To implement this 100% correctly, you would need to handle:
    • When the locations are on either side of the International Date Line
    • When the locations are near the North or South Pole
    • Adjusting for the curvature of the Earth, as “longitudinal degrees per mile” changes

Handling all of these cases requires a fair amount of code.

For your application, however, there are only 30 stores in the state of Texas. In this smaller region, the three issues in the list aren’t that important. As a result, you can reduce your requirements to:
    For a user near Texas, find (approximately) the closest store in Texas.
    
Solving this problem is easier, as you can get away with just iterating through each store and computing the Euclidean distance between the latitudes/longitudes.

### Example: Adding a Cache

We once had a Java application that frequently read objects from disk. The speed of the application was limited by these reads, so we wanted to implement caching of some sort. A typical sequence of reads looked like this:

```
read Object A
read Object A
read Object A
read Object B
read Object B
read Object C
read Object D
read Object D
```

As you can see, there were a lot of repeated accesses to the same object, so caching should definitely have helped.

When faced with this problem, our first instinct was to use a cache that discards the least recently used items. We didn’t have one available in our library, so we would have had to implement it ourselves. That wasn’t a problem though, as we’ve implemented such a data structure before (it involves both a hash table and a singly linked list—perhaps 100 lines of code in total).

However, we noticed that the repeated accesses were always in a row. So instead of implementing an LRU cache, we just implemented a one-item cache:

```
DiskObject lastUsed; // class member

DiskObject lookUp(String key) {
    if (lastUsed == null || !lastUsed.key().equals(key)) {
        lastUsed = loadDiskObject(key);
    }
    
    return lastUsed;
}
```
This got us 90% of the benefit without much coding, and the program had a small memory footprint, too.

The benefits of “removing requirements” and “solving simpler problems” can’t be overstated.
Requirements often interfere with each other in subtle ways. This means that solving half the problem might only take a quarter as much coding effort.

## Keeping Your Codebase Small

![](https://i.imgur.com/9IWbxOa.png)

When you first start a software project, and you have only one or two source files, things are great. Compiling and running the code is a snap, it’s easy to make changes, and it’s easy to remember where each function or class is defined.

Then, as the project grows, your directory fills up with more and more source files. Soon you need multiple directories to organize them all. It’s harder to remember which functions call which other functions, and tracking down bugs takes a little more work.

Eventually, you have lots of source code spread across many different directories. The project is huge, and no single person understands it all. Adding new features becomes painful, and working with the code is cumbersome and unpleasant.

What we’ve described is a natural law of the universe—as any coordinated system grows, the complexity needed to keep it glued together grows even faster.

The best way to cope is to keep your codebase as small and lightweight as possible, even as your project grows. Thus you should:

- Create as much generic “utility” code as possible to remove duplicated code. (See Chapter 10, Extracting Unrelated Subproblems.)
- Remove unused code or useless features. (See the following sidebar.)
- Keep your project compartmentalized into disconnected subprojects.
- Generally, be conscious of the “weight” of your codebase. Keep it light and nimble

> REMOVING UNUSED CODE
> Gardeners often prune plants to keep them alive and growing. Similarly, it’s a good idea to prune any unused code that’s getting in the way

> Once code is written, coders are often reluctant to delete it, because it represents a lot of real work. To delete it would mean admitting that the time spent on it was wasted. Well, get over it! This is a creative field—photographers, writers, and filmmakers don’t keep all of their work, either
> Deleting isolated functions is easy, but sometimes “unused code” is actually woven throughout your project, unbeknownst to you. Here are some examples:
> - You originally designed your system to handle international filenames, and now the code is littered with conversion code. However, that code isn’t fully functional, and your app is never used with international filenames anyhow. Why not remove this functionality?
> - ou wanted your program to work even if the system ran out of memory, so you have lots of clever logic that tries to recover from out-of-memory situations. It was a good idea, but in practice, when the system runs out of memory, your program just becomes an unstable zombie anyway—all the core features are unusable, and it’s one mouse click away from dying.
> Why not just terminate the program with a simple “The system is out of memory, sorry” and remove all this out-of-memory code?

## Be Familiar with the Libraries Around You

A lot of the time, programmers just aren’t aware that existing libraries can solve their problem. Or sometimes they’ve forgotten what a library can do. It’s important to know the capabilities of your library code so that you can make use of it.

Here’s a modest suggestion: every once in a while, spend 15 minutes reading the names of all the functions/modules/types in your standard library. These include the C++ Standard Template Library (STL), the Java API, the built-in Python modules, and others.

The goal isn’t to memorize the whole library. It’s just to get a sense of what’s available, so that next time you’re working on new code you’ll think, “Wait, this sounds similar to something I saw in the API….” We believe doing this work upfront pays off quickly, as you’ll be more inclined to use those libraries in the first place.

### Example: Lists and Sets in Python
Suppose you have a list in Python (like [2,1,2]) and you want a list of the unique elements (in this case, [2,1]). You could implement this task using a dictionary, which has a list of keys that are guaranteed to be unique:
```python
def unique(elements):
    temp = {}
    for element in elements:
        temp[element] = None # The value doesn't matter.
    return temp.keys()

unique_elements = unique([2,1,2])
```
But instead you can just use the lesser-known set type:
```
unique_elements = set([2,1,2]) # Remove duplicates
```
This object is iterable, just like a normal list. If you really want a list object again, you can just use:
```
unique_elements = list(set([2,1,2])) # Remove duplicates
```
Clearly, set is the right tool for the job here. But if you weren’t aware of the set type, you might produce code like unique() above.

### Why Reusing Libraries Is Such a Win
A commonly cited statistic is that the average software engineer produces ten shippable lines of code a day. When programmers first hear this, they balk in disbelief—“Ten lines of code? I can write that in a minute!”

The key word is shippable. Each line of code in a mature library represents a fair amount of design, debugging, rewriting, documenting, optimizing, and testing. Any line of code that’s survived this Darwinian process is very valuable. This is why reusing libraries is such a win, in both saving time and having less code to write.

## Example: Using Unix Tools Instead of Coding

When a web server frequently returns 4xx or 5xx HTTP response codes, it’s a sign of a potential problem (4xx being a client error; 5xx being a server error). So we wanted to write a program that parses a web server’s access logs and determines which URLs are causing the most errors.

The access logs typically look something like this:
```
1.2.3.4 example.com [24/Aug/2010:01:08:34] "GET /index.html HTTP/1.1" 200 ...
2.3.4.5 example.com [24/Aug/2010:01:14:27] "GET /help?topic=8 HTTP/1.1" 500 ...
3.4.5.6 example.com [24/Aug/2010:01:15:54] "GET /favicon.ico HTTP/1.1" 404 ...
...
```
Generally, they contain lines of this form:
```
browser-IP host [date] "GET /url-path HTTP/1.1" HTTP-response-code ...
```
Writing a program to find the most common url-paths with 4xx or 5xx response codes might easily take 20 lines of code in a language like C++ or Java.

Instead, in Unix, you can type this command line:
```
cat access.log | awk '{ print $5 " " $7 }' | egrep "[45]..$" \
| sort | uniq -c | sort -nr
```
which produces output like this:

```
95 /favicon.ico 404
13 /help?topic=8 500
11 /login 403
...
<count> <path> <http response code>
```

What’s great about this command line is that we’ve avoided writing any “real” code or checking anything into source control.

![](https://i.imgur.com/Tit47ju.png)

## Summary

> Adventure, excitement—a Jedi craves not these things.
> —Yoda

This chapter is about writing as little new code as possible. Each new line of code needs to be tested, documented, and maintained. Further, the more code in your codebase, the “heavier” it gets and the harder it is to develop in.

You can avoid writing new lines of code by:

- Eliminating nonessential features from your product and not overengineering
- Rethinking requirements to solve the easiest version of the problem that still gets the job done
- Staying familiar with standard libraries by periodically reading through their entire APIs





















