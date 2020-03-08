# CHAPTER FOURTEEN: Testing and Readability

![](https://i.imgur.com/UXCPCkc.png)

In this chapter, we’re going to show you simple techniques to write neat and effective tests.

Testing means different things to different people. In this chapter, we use “test” to mean any code whose sole purpose is to check the behavior of another (“real”) piece of code. We’re going to focus on the readability aspect of tests and not get into whether you should write test code before writing real code ("test-driven development”) or other philosophical aspects of test development.

## Make Tests Easy to Read and Maintain
It’s just as important for test code to be readable as it is for nontest code. Other coders will often look at the test code as unofficial documentation of how the real code works and should be used. So if the tests are easy to read, users will better understand how the real code behaves.

> KEY IDEA
> Test code should be readable so that other coders are comfortable changing or adding tests.

When test code is big and scary, here’s what happens:
- Coders are afraid to modify the real code. Oh, we don’t want to mess with that code—updating all the tests would be a nightmare!
- Coders don’t add new tests when they add new code. Over time, less and less of your module is tested, and you are no longer confident that it all works.

Instead, you want to encourage users of your code (especially you!) to be comfortable with the test code. They should be able to diagnose why a new change is breaking an existing test and feel like adding new tests is easy.

## What’s Wrong with This Test?

In our codebase, we had a function to sort and filter a list of scored search results. Here’s the function declaration:

```
// Sort 'docs' by score (highest first) and remove negative-scored documents.
void SortAndFilterDocs(vector<ScoredDocument>* docs);
```

The test for this function originally looked something like:

```
void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url = "http://example.com";
    docs[0].score = -5.0;
    docs[1].url = "http://example.com";
    docs[1].score = 1;
    
    docs[2].url = "http://example.com";
    docs[2].score = 4;
    docs[3].url = "http://example.com";
    docs[3].score = -99998.7;
    docs[4].url = "http://example.com";
    docs[4].score = 3.0;
    
    SortAndFilterDocs(&docs);
    
    assert(docs.size() == 3);
    assert(docs[0].score == 4);
    assert(docs[1].score == 3.0);
    assert(docs[2].score == 1);
}
```
There are at least eight different problems with this test code. By the end of the chapter, you’ll be able to identify and fix all of them.

## Making This Test More Readable

As a general design principle, you should hide less important details from the user, so that more important details are most prominent.

The test code from the previous section clearly violates this principle. Every detail of the test is front and center, like the unimportant minutiae of setting up a vector<ScoredDocument>. Most of the example code involves url, score, and docs[], which are just details about how the underlying C++ objects are set up, not about what this test is doing at a high level.

As a first step in cleaning this up, we could create a helper function like:

```
void MakeScoredDoc(ScoredDocument* sd, double score, string url) {
    sd->score = score;
    sd->url = url;
}
```
Using this function, our test code becomes slightly more compact

```
void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    MakeScoredDoc(&docs[0], -5.0, "http://example.com");
    MakeScoredDoc(&docs[1], 1, "http://example.com");
    MakeScoredDoc(&docs[2], 4, "http://example.com");
    MakeScoredDoc(&docs[3], -99998.7, "http://example.com");
    ...
}
```

But this isn’t good enough—there are still unimportant details in our face. For instance, the parameter "http://example.com" is just an eyesore. It’s always the same, and the exact URL doesn’t even matter—it’s just needed to fill out a valid ScoredDocument.

Another unimportant detail we’re forced to see is docs.resize(5) and &docs[0], &docs[1], and so on. Let’s change our helper function to do more work for us and call it AddScoredDoc():

```
void AddScoredDoc(vector<ScoredDocument>& docs, double score) {
    ScoredDocument sd;
    sd.score = score;
    sd.url = "http://example.com";
    docs.push_back(sd);
}
```
Using this function, our test code is even more compact:

```
void Test1() {
    vector<ScoredDocument> docs;
    AddScoredDoc(docs, -5.0);
    AddScoredDoc(docs, 1);
    AddScoredDoc(docs, 4);
    AddScoredDoc(docs, -99998.7);
    ...
}
```

This code is better, but still doesn’t pass the “highly readable and writable” test. If you wanted to add another test with a new set of scored docs, it would require a lot of copying and pasting.
So how do we go about improving it further?

### Creating the Minimal Test Statement
To improve this test code, let’s use the technique from Chapter 12, Turning Thoughts into Code. Let’s describe what our test is trying to do in plain English:

> We have a list of documents whose scores are [-5, 1, 4, -99998.7, 3]. After
> SortAndFilterDocs(), the remaining documents should have scores of [4, 3, 1], in that order.
> 

As you can see, nowhere in that description did we mention a vector<ScoredDocument>. The array of scores is what’s most important here. Ideally, our test code would look something like:
    
> CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");

We were able to boil the essence of this test down to one line of code!

This is not uncommon, though. The essence of most tests boils down to for this input/situation, expect this behavior/output. And many times this goal can be expressed in just one line. In addition to making the code very compact and readable, keeping your test statements short makes it very easy to add more test cases.

### Implementing Custom “Minilanguages

Notice that CheckScoresBeforeAfter() takes two string arguments that describe the array of scores. In later versions of C++, you can pass in array literals like this:

```
CheckScoresBeforeAfter({-5, 1, 4, -99998.7, 3}, {4, 3, 1});
```
Because we couldn’t do this at the time, we put the scores inside a string, separated by commas.
For this approach to work, CheckScoresBeforeAfter() is going to have to parse those string arguments.

In general, defining a custom minilanguage can be a powerful way to express a lot of information in a small amount of space. Other examples include printf() and regular expression libraries.

In this case, writing some helper functions to parse a comma-separated list of numbers shouldn’t be too hard. Here’s what CheckScoresBeforeAfter() would look like:

```
void CheckScoresBeforeAfter(string input, string expected_output) {
    vector<ScoredDocument> docs = ScoredDocsFromString(input);
    SortAndFilterDocs(&docs);
    string output = ScoredDocsToString(docs);
    assert(output == expected_output);
}
```

And for completeness, here are the helper functions that convert between string and vector<ScoredDocument>:

```
vector<ScoredDocument> ScoredDocsFromString(string scores) {
    vector<ScoredDocument> docs;

    replace(scores.begin(), scores.end(), ',', ' ');
    
    // Populate 'docs' from a string of space-separated scores.
    istringstream stream(scores);
    double score;
    while (stream >> score) {
        AddScoredDoc(docs, score);
    }

    return docs;
}

string ScoredDocsToString(vector<ScoredDocument> docs) {
    ostringstream stream;
    for (int i = 0; i < docs.size(); i++) {
        if (i > 0) stream << ", ";
            stream << docs[i].score;
    }

    return stream.str();
}
```

This may seem like a lot of code at first glance, but what it lets you do is incredibly powerful. Because you can write an entire test with just one call to CheckScoresBeforeAfter(), you’ll be inclined to add more tests (as we’ll be doing later in the chapter).

## Making Error Messages Readable

![](https://i.imgur.com/ulBlWwI.png)

The preceding code was nice, but what happens when that assert(output == expected_output) line fails? It produces an error message like this:
```
Assertion failed: (output == expected_output),
    function CheckScoresBeforeAfter, file test.cc, line 37.
```
Obviously, if you ever saw this error, you’d wonder, What were the values of output and expected_output?

### Using Better Versions of assert()

Fortunately, most languages and libraries have more sophisticated versions of assert() you can use. So instead of writing:
```
assert(output == expected_output);
```

you could use the Boost C++ library:
```
BOOST_REQUIRE_EQUAL(output, expected_output)
```
Now, if the test fails, you get a more detailed message like:
```
test.cc(37): fatal error in "CheckScoresBeforeAfter": critical check
    output == expected_output failed ["1, 3, 4" != "4, 3, 1"]
```
which is much more helpful.
You should use these more helpful assertion methods when they’re available. It’ll pay off every time your test fails.

> BETTER ASSERT() IN OTHER LANGUAGES

In Python, the built-in statement assert a == b produces a plain error message like:

```
File "file.py", line X, in <module>
    assert a == b
AssertionError
```

Instead, you can use the assertEqual() method in the unittest module:

```python=
class MyTestCase(unittest.TestCase):
    def testFunction(self):
        a = 1
        b = 2
        self.assertEqual(a, b)

if __name__ == '__main__':
    unittest.main()
```    
which produces an error message like:

```
File "MyTestCase.py", line 7, in testFunction
    self.assertEqual(a, b)
AssertionError: 1 != 2
```
Whichever language you’re using, there’s probably a library/framework (e.g., XUnit) available to help you. It pays to know your libraries!


### Hand-Crafted Error Messages

Using BOOST_REQUIRE_EQUAL(), we were able to get the nicer error message:

```
output == expected_output failed ["1, 3, 4" != "4, 3, 1"]
```

However, this message could be improved further. For instance, it would be useful to see the original input that triggered this failure. The ideal error message would be something like:

```
CheckScoresBeforeAfter() failed,
    Input: "-5, 1, 4, -99998.7, 3"
    Expected Output: "4, 3, 1"
    Actual Output: "1, 3, 4
```

If this is what you want, go ahead and write it!

```
void CheckScoresBeforeAfter(...) {
    ...
    if (output != expected_output) {
        cerr << "CheckScoresBeforeAfter() failed," << endl;
        cerr << "Input: \"" << input << "\"" << endl;
        cerr << "Expected Output: \"" << expected_output << "\"" << endl;
        cerr << "Actual Output: \"" << output << "\"" << endl;
        abort();
}
```
The moral of the story is that error messages should be as helpful as possible. Sometimes, printing your own message by building a “custom assert” is the best way to do this.

## Choosing Good Test Inputs

There’s an art to choosing good input values for your tests. The ones we have right now seem a bit haphazard:

```
CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");
```

How do we choose good input values? Good inputs should thoroughly test the code. But they should also be simple so that they’re easy to read

> KEY IDEA
> In general, you should pick the simplest set of inputs that completely exercise the code.

For example, suppose we had just written:
```
CheckScoresBeforeAfter("1, 2, 3", "3, 2, 1");
```
Although this test is simple, it doesn’t test the “filter negative scores” behavior of SortAndFilterDocs(). If there were a bug in that part of the code, this input wouldn’t trigger it.

On the other extreme, suppose we wrote our test like this:
```
CheckScoresBeforeAfter("123014, -1082342, 823423, 234205, -235235",
                       "823423, 234205, 123014");
```

These values are needlessly complex. (And they don’t even test the code thoroughly.)

### Simplifying the Input Values

So what can we do to improve these input values?
```
CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");
```

Well, the first thing you probably noticed is the very “loud” value -99998.7. That value was just meant to be “any negative number,” so a simpler value is just -1. (If -99998.7 was meant to be “a very negative number,” a better value would have been something crisp like -1e100.)

> KEY IDEA
> Prefer clean and simple test values that still get the job done.

The other values in our test aren’t too bad, but while we’re here, we can reduce them to the simplest integers possible. Also, only one negative value is needed to test that negative values are removed. Here’s a new version of our test:
```
CheckScoresBeforeAfter("1, 2, -1, 3", "3, 2, 1");
```
We’ve simplified the test values without making them any less effective.

> LARGE “SMASHER” TESTS
![](https://i.imgur.com/GwCx43o.png)

There is definitely value in testing your code against large, crazy inputs. For instance, you might be tempted to include a test like:
```
CheckScoresBeforeAfter("100, 38, 19, -25, 4, 84, [lots of values] ...",
                       "100, 99, 98, 97, 96, 95, 94, 93, ...");
```
Large inputs like these do a good job of exposing bugs such as buffer overruns or others you might not expect.

But code like this is big and scary to look at and not completely effective in stress-testing the code. Instead, it’s more effective to construct large inputs programmatically, constructing a large input of (say) 100,000 values.

### Multiple Tests of Functionality 

Rather than construct a single “perfect” input to thoroughly exercise your code, it’s often easier, more effective, and more readable to write multiple smaller tests.

Each test should push your code in a certain direction, trying to find a particular bug. For example, here are four tests for SortAndFilterDocs():
```
CheckScoresBeforeAfter("2, 1, 3", "3, 2, 1"); // Basic sorting
CheckScoresBeforeAfter("0, -0.1, -10", "0"); // All values < 0 removed
CheckScoresBeforeAfter("1, -2, 1, -2", "1, 1"); // Duplicates not a problem
CheckScoresBeforeAfter("", ""); // Empty input OK
```

There are even more tests you could write if you wanted to be extremely thorough. Having separate test cases also makes it easier for the next person working on the code. If someone accidentally introduces a bug, the test failure will pinpoint the specific test that failed

## Naming Test Functions

Test code is typically organized into functions—one for each method and/or situation you’re testing. For instance, the code testing SortAndFilterDocs() was inside a function named Test1():
```
void Test1() {
...
}
```

Picking a good name for a test function can seem tedious and irrelevant, but don’t resort to meaningless names like Test1(), Test2(), and the like.

Instead, you should use the name to describe details about the test. In particular, it’s handy if the person reading the test code can quickly figure out:
- The class being tested (if any)
- The function being tested
- The situation or bug being tested
- 
A simple approach to construct a good test function name is to just concatenate that information together, possibly with a “Test_” prefix.

For instance, instead of naming it Test1(), we can use the  Test_<FunctionName>() format:
```
void Test_SortAndFilterDocs() {
...
}
```
    
Depending on how sophisticated this test is, you might consider a separate test function for each situation being tested. You could use the Test_<FunctionName>_<Situation>() format:

```
void Test_SortAndFilterDocs_BasicSorting() {
...
}

void Test_SortAndFilterDocs_NegativeValues() {
...
}
...
```

Don’t be afraid of having a long or clunky name here. This isn’t a function that will be called throughout your codebase, so the reasons for avoiding long function names don’t apply. The test function name is effectively acting like a comment. Also, if that test fails, most testing frameworks will print out the name of the function where the assertion failed, so a descriptive name is especially helpful.

Note that if you’re using a testing framework, there might already be rules or conventions on how methods are named. For instance, the Python unittest module expects test method names to start with “test.”

When it comes to naming helper functions in your test code, it’s useful to highlight whether the function does any assertions itself or is just an ordinary “test-unaware” helper. For instance, in this chapter, any helper function that calls assert() is named Check...(). But the function AddScoredDoc() was named just like an ordinary helper function.

## What Was Wrong with That Test?

At the beginning of the chapter, we claimed there were at least eight things wrong with this test:

```
void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url = "http://example.com";
    docs[0].score = -5.0;
    docs[1].url = "http://example.com";
    docs[1].score = 1;
    docs[2].url = "http://example.com";
    docs[2].score = 4;
    docs[3].url = "http://example.com";
    docs[3].score = -99998.7;
    docs[4].url = "http://example.com";
    docs[4].score = 3.0;
    SortAndFilterDocs(&docs);
    assert(docs.size() == 3);
    assert(docs[0].score == 4);
    assert(docs[1].score == 3.0);
    assert(docs[2].score == 1);
}
```
Now that we’ve learned some techniques for writing better tests, let’s identify them:
    1. The test is very long and full of unimportant details. You can describe what this test is doing in one sentence, so the test statement shouldn’t be much longer.
    2. Adding another test isn’t easy. You’d be tempted to copy/paste/modify, which would make the code even longer and full of duplication.
    3. The test failure messages aren’t very useful. If this test fails, it will just say Assertion failed: docs.size() == 3, which doesn’t give you enough information to debug it further.
    4. The test tries to test everything at once. It’s trying to test both the negative filtering and the sorting functionality. It would be more readable to break this into multiple tests.
    5. The test inputs aren’t simple. In particular, the example score -99998.7 is “loud” and gets your attention even though there isn’t any significance to that specific value. A simpler negative value would suffice.
    6. The test inputs don’t thoroughly exercise the code. For example, it doesn’t test when the score is 0. (Would that document be filtered or not?)
    7. It doesn’t test other extreme inputs, such as an empty input vector, a very large vector, or one with duplicate scores.
    8. The name Test1() is meaningless—the name should describe the function or situation being tested

## Test-Friendly Development

Some code is easier to test than other code. Ideal code to test has a well-defined interface, doesn’t have much state or other “setup,” and doesn’t have much hidden data to inspect.

If you write your code knowing you’ll be writing a test for it later, a funny thing happens: you start designing your code so that it’s easy to test! Fortunately, coding this way also means that you create better code in general. Test-friendly designs often lead naturally to wellorganized code, with separate parts to do separate things

> TEST-DRIVEN DEVELOPMENT
> Test-driven development (TDD) is a programming style where you write the tests before you write the real code. TDD proponents believe this process profoundly improves the quality of the nontest code, much more so than if you write the tests after writing the code.
> 
> This is a hotly debated topic that we won’t get into. At the very least, we’ve found that just keeping testing in mind while writing code helps make the code better.
> 
> But regardless of whether you employ TDD, the end result is that you have code that tests other code.
> The goal of this chapter is to help you make your tests easier to read and write.

Of all the ways to break up a program into classes and methods, the most decoupled ones are usually the easiest to test. On the other hand, let’s say your program is very interconnected, with many method calls between your classes and lots of parameters for all the methods. Not only would that program have hard-to-understand code, but the test code would be just as ugly, and hard to read and write.

Having lots of “external” components (global variables that need to be initialized, libraries or config files that need to be loaded, etc.) also makes it more annoying to write tests.

Generally, if you’re designing your code and realize, Hmm, this is going to be a nightmare to test, that’s a good reason to stop and rethink the design. Table 14-1 shows some typical testing and design problems.

TABLE 14-1. Characteristics of less testable code, and how this leads to problems with design



| Characteristic | Testability problem | Design problem |
| -------- | -------- | -------- |
| Use of global variables     | All the global state needs to reset for every test (otherwise, different tests can interfere with each other).     | Hard to understand which functions have what side effects. Can’t think about each function in isolation; need to consider the whole program to understand if everything works     |
| Code depends on a lot of external components     | It’s harder to write any tests because there’s so much scaffolding to set up first. Tests are less fun to write, so people avoid writing tests.     | System is more likely to fail when one of the dependencies fails. It's harder to understand what impact any given change might make. It's harder to refactor classes. System has more failure modes and recovery paths to think about     |
| Code has nondeterministic behavior     | Tests are flaky and unreliable. Tests that occasionally fail end up being ignored.     | The program is more likely to have race conditions or other nonreproducible bugs. The program is harder to reason about. Bugs in production are very difficult to track down and fix.     |


On the other hand, if you have a design that's easy to write tests for, that’s a good sign. Table 14-2 lists some beneficial testing and design characteristics.

On the other hand, if you have a design that's easy to write tests for, that’s a good sign.
Table 14-2 lists some beneficial testing and design characteristics.

| Characteristic | Testability benefit | Design Testability benefit |
| -------- | -------- | -------- |
| Classes have little or no internal state     | Tests are easier to write because there is less setup needed to test a method and less hidden state to inspect.     | Classes with less state are simpler and easier to understand     |
| Classes/functions only do one thing     | Fewer test cases are required to fully test it.     | Smaller/simpler components are more modular, and the system is generally more decoupled     |
| Classes depend on few other classes; high decoupling     | Each class can be tested independently (much easier than testing multiple classes at once).     | System can be developed in parallel. Classes can be easily modified or removed without disrupting the rest of the system.     |
| Functions have simple, well-defined interfaces     | There are well-defined behaviors to test for. Simple interfaces take less work to test.     | Interfaces are easier for coders to learn and are more likely to be reused.     |

## Going Too Far

It’s also possible to focus too much on testing. Here are some examples:
- Sacrificing the readability of your real code, for the sake of enabling tests. Designing your real code to be testable should be a win-win situation: your real code becomes simpler and more decoupled, and your tests are easy to write. But if you have to insert lots of ugly plumbing into your real code just so you can test it, something’s wrong.
- Being obsessive about 100% test coverage. Testing the first 90% of your code is often less work than testing that last 10%. That last 10% might involve user interface, or dumb error cases, where the cost of the bug isn’t really that high and the effort to test it just isn’t worth it. 
    The truth is that you’ll never get 100% coverage anyhow. If it’s not a missed bug, it might be a missed feature or you might not realize that the spec should be changed.
    Depending on how costly your bugs are, there’s a sweet spot of how much development time it’s worth spending on test code. If you’re building a website prototype, it might not be worth writing any test code at all. On the other hand, if you’re writing a controller for a spaceship or medical device, testing is probably your main focus.
- Letting testing get in the way of product development. We’ve seen situations where testing, which should be just one aspect of a project, dominates the whole project. Testing becomes some sort of god to be appeased, and coders just go through the rituals and motions without realizing that their precious engineering time might be better spent elsewhere.

## Summary

In test code, readability is still very important. If your tests are very readable, they will in turn be very writable, so people will add more of them. Also, if you design your real code to be easy to test, your code will have a better design overall

Here are specific points on how to improve your tests:
- The top level of each test should be as concise as possible; ideally, each test input/output an be described in one line of code.

- If your test fails, it should emit an error message that makes the bug easy to track down and fix.
- Use the simplest test inputs that completely exercise your code.
- Give your test functions a fully descriptive name so it’s clear what each is testing. Instead of Test1(), use a name like Test_<FunctionName>_<Situation>.

And above all, make it easy to modify and add new tests.















