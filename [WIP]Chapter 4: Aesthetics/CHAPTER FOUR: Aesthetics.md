# CHAPTER FOUR: Aesthetics

![](https://i.imgur.com/5QjKQwO.png)

A lot of thought goes into the layout of a magazine—the length of the paragraphs, the width of the columns, the order of the articles, and what goes on the cover. A good magazine makes it easy to skip around from page to page, but also easy to read straight through.

Good source code should be just as “easy on the eyes.” In this chapter, we’ll show how good use of spacing, alignment, and ordering can make your code easier to read.

Specifically, there are three principles we use:
- Use consistent layout, with patterns the reader can get used to.
- Make similar code look similar.
- Group related lines of code into blocks.

> AESTHETICS VS. DESIGN
> In this chapter, we’re concerned only with simple “aesthetic” improvements you can make to your code. These types of changes are easy to make and often improve readability quite a bit. There are times when larger refactoring of your code (such as splitting out new functions or classes) can help even more. Our view is that good aesthetics and good design are independent ideas; ideally you should strive for both.
> 

## Why Do Aesthetics Matter?

![](https://i.imgur.com/KPrfKVl.png)
Imagine if you had to use this class:
```
class StatsKeeper {
public:
// A class for keeping track of a series of doubles
    void Add(double d); // and methods for quick statistics about them
    private: int count; /* how many so far
*/ public:
    double Average();
private: double minimum;
list<double>
    past_items
        ;double maximum;
};
```
It would take you a lot longer to understand it than if you had this cleaner version instead:
```
// A class for keeping track of a series of doubles
// and methods for quick statistics about them.
class StatsKeeper {
    public:
        void Add(double d);
        double Average();
    private:
        list<double> past_items;
        int count; // how many so far
        
        double minimum;
        double maximum;
};
```
Obviously it’s easier to work with code that’s aesthetically pleasing. If you think about it, most of your time programming is spent looking at code! The faster you can skim through your code, the easier it is for everyone to use it.

## Rearrange Line Breaks to Be Consistent and Compact

Suppose you were writing Java code to evaluate how your program behaves under various network connection speeds. You have a TcpConnectionSimulator that takes four parameters in the constructor:
    1. The speed of the connection (Kbps)
    2. The average latency (ms)
    3. The “jitter” of the latency (ms)
    4. The packet loss (percentage)

Your code needed three different TcpConnectionSimulator instances:

```
public class PerformanceTester {
    public static final TcpConnectionSimulator wifi = new TcpConnectionSimulator(
        500, /* Kbps */
        80, /* millisecs latency */
        200, /* jitter */
        1 /* packet loss % */);
        
    public static final TcpConnectionSimulator t3_fiber =
        new TcpConnectionSimulator(
            45000, /* Kbps */
            10, /* millisecs latency */
            0, /* jitter */
            0 /* packet loss % */);
            
    public static final TcpConnectionSimulator cell = new TcpConnectionSimulator(
        100, /* Kbps */
        400, /* millisecs latency */
        250, /* jitter */
        5 /* packet loss % */);
}
```

This example code needed a lot of extra line breaks to fit inside an 80-character limit (this was the coding standard at your company). Unfortunately, that made the definition of t3_fiber look different from its neighbors. The “silhouette” of this code is odd, and it draws attention to t3_fiber for no reason. This doesn’t follow the principle that “similar code should look similar.”

To make the code look more consistent, we could introduce extra line breaks (and line up the comments while we’re at it):

```
public class PerformanceTester {
  public static final TcpConnectionSimulator wifi =
    new TcpConnectionSimulator(
      500, /* Kbps */
      80, /* millisecs latency */
      200, /* jitter */
      1 /* packet loss % */);

  public static final TcpConnectionSimulator t3_fiber =
    new TcpConnectionSimulator(
      45000, /* Kbps */
      10, /* millisecs latency */
      0, /* jitter */
      0 /* packet loss % */);
      
  public static final TcpConnectionSimulator cell =
    new TcpConnectionSimulator(
      100, /* Kbps */
      400, /* millisecs latency */
      250, /* jitter */
      5 /* packet loss % */);
}
```
This code has a nice consistent pattern, and is easier to scan through. But unfortunately, it uses a lot of vertical space. It also duplicates each comment three times.
Here’s a more compact way to write the class:

```
public class PerformanceTester {
  // TcpConnectionSimulator(throughput, latency, jitter, packet_loss)
  // [Kbps] [ms] [ms] [percent]

  public static final TcpConnectionSimulator wifi =
    new TcpConnectionSimulator(500, 80, 200, 1);

  public static final TcpConnectionSimulator t3_fiber =
    new TcpConnectionSimulator(45000, 10, 0, 0);

  public static final TcpConnectionSimulator cell =
    new TcpConnectionSimulator(100, 400, 250, 5);
}
```
We’ve moved the comments up to the top and then put all the parameters on one line. Now, even though the comment isn’t right next to each number, the “data” is lined up in a more compact table.

## Use Methods to Clean Up Irregularity
Suppose you had a personnel database that provided the following function:
```
// Turn a partial_name like "Doug Adams" into "Mr. Douglas Adams".
// If not possible, 'error' is filled with an explanation.
string ExpandFullName(DatabaseConnection dc, string partial_name, string* error);
```
and this function was tested with a series of examples:

```
DatabaseConnection database_connection;
string error;
assert(ExpandFullName(database_connection, "Doug Adams", &error)
== "Mr. Douglas Adams");
assert(error == "");
assert(ExpandFullName(database_connection, " Jake Brown ", &error)
== "Mr. Jacob Brown III");
assert(error == "");
assert(ExpandFullName(database_connection, "No Such Guy", &error) == "");
assert(error == "no match found");
assert(ExpandFullName(database_connection, "John", &error) == "");
assert(error == "more than one result");
```
This code is not aesthetically pleasing. Some of the lines are so long that they wrap to the next line. The silhouette of this code is ugly and there is no consistent pattern.

But this is a case where rearranging the line breaks can only do so much. The bigger problem is that there are a lot of repeated strings like “assert(ExpandFullName(database_connection...,” and “error” that are getting in the way. To really improve this code, we need a helper method so the code can look like this:

```
CheckFullName("Doug Adams", "Mr. Douglas Adams", "");
CheckFullName(" Jake Brown ", "Mr. Jake Brown III", "");
CheckFullName("No Such Guy", "", "no match found");
CheckFullName("John", "", "more than one result");
```
Now it’s more clear that there are four tests happening, each with different parameters. Even though all the “dirty work” is inside CheckFullName(), that function isn’t so bad, either:

```
void CheckFullName(string partial_name,
                   string expected_full_name,
                   string expected_error) {
  // database_connection is now a class member
  string error;
  string full_name = ExpandFullName(database_connection, partial_name, &error);
  assert(error == expected_error);
  assert(full_name == expected_full_name);
}
```

Even though our goal was just to make the code more aesthetically pleasing, this change has a number of other side benefits:
- It eliminates a lot of the duplicated code from before, making the code more compact.
- The important parts of each test case (the names and error strings) are now by themselves, in plain sight. Before, these strings were interspersed with tokens like database_connection and error, which made it hard to “take in” the code in one eyeful.
- Adding new tests should be much easier now.

The moral of the story is that making code “look pretty” often results in more than just surface improvements—it might help you structure your code better

## Use Column Alignment When Helpful

Straight edges and columns make it easier for readers to scan through text.

Sometimes you can introduce “column alignment” to make the code easier to read. For example, in the previous section, you could space out and line up the arguments to
```
CheckFullName():
CheckFullName("Doug Adams" , "Mr. Douglas Adams" , "");
CheckFullName(" Jake Brown ", "Mr. Jake Brown III", "");
CheckFullName("No Such Guy" , "" , "no match found");
CheckFullName("John" , "" , "more than one result");
```
In this code, it’s easier to distinguish the second and third arguments to CheckFullName().

Here is a simple example with a large group of variable definitions:
```
# Extract POST parameters to local variables
details = request.POST.get('details')
location = request.POST.get('location')
phone = equest.POST.get('phone')
email = request.POST.get('email')
url = request.POST.get('url')
```

As you may have noticed, the third definition has a typo (equest instead of request). Mistakes like these are more pronounced when everything is lined up so neatly.

In the wget codebase, the available command-line options (more than 100 of them) were listed as follows:

```
commands[] = {
...
  { "timeout",           NULL,                   cmd_spec_timeout },
  { "timestamping",      &opt.timestamping,      cmd_boolean },
  { "tries",             &opt.ntry,              cmd_number_inf },
  { "useproxy",          &opt.use_proxy,         cmd_boolean },
  { "useragent",         NULL,                   cmd_spec_useragent },
...
};
```
This approach made the list very easy to skim through and jump from one column to the next.

## Should You Use Column Alignment?

Column edges provide “visual handrails” that make it easier to scan through. It’s a good example of “make similar code look similar.”

But some programmers don’t like it. One reason is that it takes more work to set up and maintain the alignment. Another reason is it creates a larger “diff” when making changes—a one-line change might cause five other lines to change (mostly just whitespace).

Our advice is to try it. In our experience, it doesn’t take as much work as programmers fear. And if it does, you can simply stop.

## Pick a Meaningful Order, and Use It Consistently

There are many cases where the order of code doesn’t affect the correctness. For instance, these five variable definitions could be written in any order:

```
    details = request.POST.get('details')
    location = request.POST.get('location')
    phone = request.POST.get('phone')
    email = request.POST.get('email')
    url = request.POST.get('url')
```
In situations like this, it’s helpful to put them in some meaningful order, not just random. Here are some ideas:
- Match the order of the variables to the order of the <input> fields on the corresponding HTML form.
- Order them from “most important” to “least important.”
- Order them alphabetically.

Whatever the order, you should use the same order throughout your code. It would be confusing to change the order later on:

```
if details: rec.details = details
if phone: rec.phone = phone # Hey, where did 'location' go?
if email: rec.email = email
if url: rec.url = url
if location: rec.location = location # Why is 'location' down here now?
```

## Organize Declarations into Blocks

The brain naturally thinks in terms of groups and hierarchies, so you can help a reader quickly digest your code by organizing it that way.
For example, here’s a C++ class for a frontend server, with all its method declarations:

```
class FrontendServer {
  public:
    FrontendServer();
    void ViewProfile(HttpRequest* request);
    void OpenDatabase(string location, string user);
    void SaveProfile(HttpRequest* request);
    string ExtractQueryParam(HttpRequest* request, string param);
    void ReplyOK(HttpRequest* request, string html);
    void FindFriends(HttpRequest* request);
    void ReplyNotFound(HttpRequest* request, string error);
    void CloseDatabase(string location);
    ~FrontendServer();
};
```
This code isn’t horrible, but the layout certainly doesn’t help the reader digest all those methods. Instead of listing all the methods in one giant block, they should be logically organized into groups, like this:

```
class FrontendServer {
  public:
    FrontendServer();
    ~FrontendServer();

    // Handlers
    void ViewProfile(HttpRequest* request);
    void SaveProfile(HttpRequest* request);
    void FindFriends(HttpRequest* request);

    // Request/Reply Utilities
    string ExtractQueryParam(HttpRequest* request, string param);
    void ReplyOK(HttpRequest* request, string html);
    void ReplyNotFound(HttpRequest* request, string error);

    // Database Helpers
    void OpenDatabase(string location, string user);
    void CloseDatabase(string location);
};
```

This version is much easier to digest. It’s also easier to read, even though there are more lines of code. The reason is that you can quickly figure out the four high-level sections and then read the details of each section when it’s necessary

## Break Code into “Paragraphs”
Written text is broken into paragraphs for a number of reasons:
- It’s a way to group similar ideas together and set them apart from other ideas.
- It provides a visual “stepping stone”—without it, it’s easy to lose your place on the page.
- It facilitates navigation from one paragraph to another.
Code should be broken into “paragraphs” for the same reasons. For example, no one likes to read a giant lump of code like this:

```python
# Import the user's email contacts, and match them to users in our system.
# Then display a list of those users that he/she isn't already friends with.
def suggest_new_friends(user, email_password):
  friends = user.friends()
  friend_emails = set(f.email for f in friends)
  contacts = import_contacts(user.email, email_password)
  contact_emails = set(c.email for c in contacts)
  non_friend_emails = contact_emails - friend_emails
  suggested_friends = User.objects.select(email__in=non_friend_emails)
  display['user'] = user
  display['friends'] = friends
  display['suggested_friends'] = suggested_friends
  return render("suggested_friends.html", display)

```
It may not be obvious, but this function goes through a number of distinct steps. So it would be especially useful to break up those lines of code into paragraphs:

```python
def suggest_new_friends(user, email_password):
  # Get the user's friends' email addresses.
  friends = user.friends()
  friend_emails = set(f.email for f in friends)
  # Import all email addresses from this user's email account.
  contacts = import_contacts(user.email, email_password)
  contact_emails = set(c.email for c in contacts)
  # Find matching users that they aren't already friends with.
  non_friend_emails = contact_emails - friend_emails
  suggested_friends = User.objects.select(email__in=non_friend_emails)
  # Display these lists on the page.
  display['user'] = user
  display['friends'] = friends
  display['suggested_friends'] = suggested_friends
  return render("suggested_friends.html", display)
```

Notice that we also added a summary comment to each paragraph, which also helps the reader skim through the code. (See Chapter 5, Knowing What to Comment.)

As with written text, there may be multiple ways to break the code up, and programmers may prefer longer or shorter paragraphs.

## Personal Style versus Consistency

There are certain aesthetic choices that just boil down to personal style. For instance, where the open brace for a class definition should go:

```
class Logger {
  ...
};
```
or
```
class Logger
{
  ...
};
```

If one of these styles is chosen over the other, it doesn’t substantially affect the readability of the codebase. But if these two styles are mixed throughout the code, it does affect the readability.

We’ve worked on many projects where we felt like the team was using the “wrong” style, but we followed the project conventions because we knew that consistency is far more important.

> K E Y I D E A
> Consistent style is more important than the “right” style.
> 

![](https://i.imgur.com/bWJk60v.png)

## Summary

Everyone prefers to read code that’s aesthetically pleasing. By “formatting” your code in a consistent, meaningful way, you make it easier and faster to read.

Here are specific techniques we discussed:
- If multiple blocks of code are doing similar things, try to give them the same silhouette.
- Aligning parts of the code into “columns” can make code easy to skim through.
- If code mentions A, B, and C in one place, don’t say B, C, and A in another. Pick a meaningful order and stick with it.
- Use empty lines to break apart large blocks into logical “paragraphs.











