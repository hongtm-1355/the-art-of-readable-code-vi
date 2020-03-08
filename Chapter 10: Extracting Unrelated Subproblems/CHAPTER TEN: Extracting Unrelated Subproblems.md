# CHAPTER TEN: Extracting Unrelated Subproblems

![](https://i.imgur.com/jVGgXnu.png)

Engineering is all about breaking down big problems into smaller ones and putting the solutions for those problems back together. Applying this principle to code makes it more robust and easier to read.

The advice for this chapter is to aggressively identify and extract unrelated subproblems. Here’s what we mean:
    1. Look at a given function or block of code, and ask yourself, “What is the high-level goal of this code?”
    2. For each line of code, ask, “Is it working directly to that goal? Or is it solving an unrelated subproblem needed to meet it?”
    3. If enough lines are solving an unrelated subproblem, extract that code into a separate function.
    4. 
Extracting code into separate functions is something you probably do every day. But for this chapter, we decided to focus on the specific case of extracting unrelated subproblems, where the extracted code is blissfully unaware of why it’s being called.

As you’ll see, it’s an easy technique to apply but can improve your code substantially. Yet for some reason, many programmers don’t use this technique enough. The trick is to actively look for these unrelated subproblems.

In this chapter, we will go through a variety of examples that illustrate this technique for different situations you might run into.

## Introductory Example: findClosestLocation()

The high-level goal of the following JavaScript code is, find the location that’s closest to a given point (don’t get bogged down by the advanced geometry, which we’ve italicized):

```javascript=
// Return which element of 'array' is closest to the given latitude/longitude.
// Models the Earth as a perfect sphere.
var findClosestLocation = function (lat, lng, array) {
    var closest;
    var closest_dist = Number.MAX_VALUE;
    for (var i = 0; i < array.length; i += 1) {
        // Convert both points to radians.
        var lat_rad = radians(lat);
        var lng_rad = radians(lng);
        var lat2_rad = radians(array[i].latitude);
        var lng2_rad = radians(array[i].longitude);
        
        // Use the "Spherical Law of Cosines" formula.
        var dist = Math.acos(Math.sin(lat_rad) * Math.sin(lat2_rad) +
                             Math.cos(lat_rad) * Math.cos(lat2_rad) *
                             Math.cos(lng2_rad - lng_rad));
        if (dist < closest_dist) {
            closest = array[i];
            closest_dist = dist;
        }
    }
    return closest;
};
```
Most of the code inside the loop is working on an unrelated subproblem: Compute the spherical distance between two lat/long points. Because there is so much of that code, it makes sense to extract it into a separate spherical_distance() function:
```javascript=
var spherical_distance = function (lat1, lng1, lat2, lng2) {
    var lat1_rad = radians(lat1);
    var lng1_rad = radians(lng1);
    var lat2_rad = radians(lat2);
    var lng2_rad = radians(lng2);
    // Use the "Spherical Law of Cosines" formula.
    return Math.acos(Math.sin(lat1_rad) * Math.sin(lat2_rad) +
                     Math.cos(lat1_rad) * Math.cos(lat2_rad) *
                     Math.cos(lng2_rad - lng1_rad));
};
```
Now the remaining code becomes:

```javascript=
var findClosestLocation = function (lat, lng, array) {
    var closest;
    var closest_dist = Number.MAX_VALUE;
    for (var i = 0; i < array.length; i += 1) {
        var dist = spherical_distance(lat, lng, array[i].latitude, array[i].longitude);
        if (dist < closest_dist) {
            closest = array[i];
            closest_dist = dist;
        }
    }
    return closest;
};
```
This code is far more readable because the reader can focus on the high-level goal without getting distracted by intense geometry equations.

As an added bonus, spherical_distance() will be easier to test in isolation. And spherical_distance() is the type of function that could be reused in the future. This is why it’s an “unrelated” subproblem—it’s completely self-contained and unaware of how applications are using it.

## Pure Utility Code

There is a core set of basic tasks that most programs do, such as manipulating strings, using hash tables, and reading/writing files.

Often, these “basic utilities” are implemented by the built-in libraries in your programming language. For instance, if you want to read the entire contents of a file, in PHP you can call file_get_contents("filename") , or in Python, you can do open("filename").read() .

But sometimes you have to fill in the gaps yourself. In C++, for instance, there is no succinct way to read an entire file. Instead, you inevitably end up writing code like this:

```
ifstream file(file_name);
// Calculate the file's size, and allocate a buffer of that size.
file.seekg(0, ios::end);
const int file_size = file.tellg();
char* file_buf = new char [file_size];
// Read the entire file into the buffer.
file.seekg(0, ios::beg);
file.read(file_buf, file_size);
file.close();
...
```

This is a classic example of an unrelated subproblem that should be extracted into a new function like ReadFileToString() . Now, the rest of your codebase can act as if C++ did have a ReadFileToString() function.

In general, if you find yourself thinking, “I wish our library had an XYZ() function,” go ahead and write it! (Assuming it doesn’t already exist.) Over time, you’ll build up a nice collection of utility code that can be used across projects.

## Other General-Purpose Code

When debugging JavaScript, programmers often use alert() to pop up a message box that displays some information to the programmer, the Web’s version of “ printf() debugging.” For example, the following function call submits data to the server using Ajax and then displays the dictionary returned from the server:

```javascript=
ajax_post({
    url: 'http://example.com/submit',
    data: data,
    on_success: function (response_data) {
        var str = "{\n";
        for (var key in response_data) {
            str += " " + key + " = " + response_data[key] + "\n";
        }
        alert(str + "}");
        // Continue handling 'response_data' ...
    }
});
```
The high-level goal of this code is, Make an Ajax call to the server, and handle the response. But a lot of the code is solving the unrelated subproblem, Pretty-print a dictionary. It’s easy to extract that code into a function like format_pretty(obj):

```javascript=
var format_pretty = function (obj) {
    var str = "{\n";
    for (var key in obj) {
        str += " " + key + " = " + obj[key] + "\n";
    }
    return str + "}";
};
```

### Unexpected Benefits

There are a lot of reasons why extracting format_pretty() is a good idea. It makes the calling code simpler, and format_pretty() is a handy function to have around.

But there’s another great reason that’s not as obvious: it’s easier to improve format_pretty() when the code is by itself. When you’re working on a smaller function in isolation, it feels easier to add features, improve reliability, take care of edge cases, and so on.

Here are some cases format_pretty(obj) doesn’t handle:
• It expects obj to be an object. If instead it’s a plain string (or undefined ), the current code will throw an exception.
• It expects each value of obj to be a simple type. If instead it contains nested objects, the current code will display them as [object Object] , which isn’t very pretty.

Before we separated format_pretty() into its own function, it would have felt like a lot of work to make all these improvements. (In fact, recursively printing nested objects is very difficult without a separate function.)

But now, adding this functionality is easy. Here’s what the improved code looks like:

```javascript=
var format_pretty = function (obj, indent) {
    // Handle null, undefined, strings, and non-objects.
    if (obj === null) return "null";
    if (obj === undefined) return "undefined";
    if (typeof obj === "string") return '"' + obj + '"';
    if (typeof obj !== "object") return String(obj);
    if (indent === undefined) indent = "";
    
    // Handle (non-null) objects.
    var str = "{\n";
    for (var key in obj) {
        str += indent + " " + key + " = ";
        str += format_pretty(obj[key], indent + " ") + "\n";
    }
    return str + indent + "}";
};
```
This covers the shortcomings listed previously and produces output like this:

```javascript=
{
    key1 = 1
    key2 = true
    key3 = undefined
    key4 = null
    key5 = {
        key5a = {
            key5a1 = "hello world"
        }
    }
}
```
## Create a Lot of General-Purpose Code

The functions ReadFileToString() and format_pretty() are great examples of unrelated subproblems. They’re so basic and widely applicable that they are likely to be reused across projects. Codebases often have a special directory for code like this (e.g., util/) so that it can be easily shared.

General-purpose code is great because it’s completely decoupled from the rest of your project. Code like this is easier to develop, easier to test, and easier to understand. If only all of your code could be like this!

Think about many of the powerful libraries and systems that you use, such as SQL databases, JavaScript libraries, and HTML templating systems. You don’t have to worry about their internals—those codebases are completely isolated from your project. As a result, your project’s codebase remains small.

The more of your project you can break away as isolated libraries, the better, because the rest of your code will be smaller and easier to think about.

> IS THIS TOP-DOWN OR BOTTOM-UP PROGRAMMING?
>
>Top-down programming is a style where the highest-level modules and functions are designed first and the lower-level functions are implemented as needed to support them.

>Bottom-up programming tries to anticipate and solve all the subproblems first and then build the higher-level components using these pieces.

>This chapter isn’t advocating one method over the other. Most programming involves a combination of both. What’s important is the end result: subproblems are removed and tackled separately.

## Project-Specific Functionality
Ideally, the subproblems you extract would be completely project-agnostic. But even if they’re not, that’s okay. Breaking off subproblems still works wonders.

Here is an example from a business reviews website. This Python code creates a new Business object and sets its name, url, and date_created:

```
business = Business()
business.name = request.POST["name"]
url_path_name = business.name.lower()
url_path_name = re.sub(r"['\.]", "", url_path_name)
url_path_name = re.sub(r"[^a-z0-9]+", "-", url_path_name)
url_path_name = url_path_name.strip("-")
business.url = "/biz/" + url_path_name
business.date_created = datetime.datetime.utcnow()
business.save_to_database()
```
The url is supposed to be a “clean” version of the name. For example, if the name is “A.C. Joe’s Tire & Smog, Inc.,” the url will be "/biz/ac-joes-tire-smog-inc".

The unrelated subproblem in this code is: Turn a name into a valid URL. We can extract this code quite easily. While we’re at it, we can also precompile the regular expressions (and give them readable names):

```
CHARS_TO_REMOVE = re.compile(r"['\.]+")
CHARS_TO_DASH = re.compile(r"[^a-z0-9]+")
def make_url_friendly(text):
    text = text.lower()
    text = CHARS_TO_REMOVE.sub('', text)
    text = CHARS_TO_DASH.sub('-', text)
    return text.strip("-")
```
Now the original code has a much more “regular” pattern:

```
business = Business()
business.name = request.POST["name"]
business.url = "/biz/" + make_url_friendly(business.name)
business.date_created = datetime.datetime.utcnow()
business.save_to_database()
```
This code requires far less effort to read because you aren’t distracted by the regular expressions and deep string manipulation.

Where should you put the code for make_url_friendly()? It seems like a fairly general function, so it might make sense to put it in a separate util/ directory. On the other hand, those regular expressions were designed with U.S. business names in mind, so perhaps the code should stay in the same file where it’s used. It actually doesn’t matter that much, and you can easily move the definition later on. What’s more important is that make_url_friendly() was extracted at all.

## Simplifying an Existing Interface
Everybody loves when a library offers a clean interface—one that takes few arguments, doesn’t need much setup, and generally requires little effort to use. It makes your code look elegant: simple and powerful at the same time.

But if an interface you’re using isn’t clean, you can still make your own “wrapper” functions that are.

For example, dealing with browser cookies in JavaScript is far from ideal. Conceptually, cookies are a set of name/value pairs. But the interface the browser provides presents a single document.cookie string whose syntax is:
```
name1=value1; name2=value2; ...
```
To find the cookie you want, you’re forced to parse this giant string yourself. Here’s an example of code that reads the value for the cookie named "max_results":
```javascript=
var max_results;
var cookies = document.cookie.split(';');
for (var i = 0; i < cookies.length; i++) {
    var c = cookies[i];
    c = c.replace(/^[ ]+/, ''); // remove leading spaces
    if (c.indexOf("max_results=") === 0)
        max_results = Number(c.substring(12, c.length));
}
```
Wow, that’s some ugly code. Clearly, there’s a get_cookie() function waiting to be made so that we can just write:
```
var max_results = Number(get_cookie("max_results"));
```
Creating or changing a cookie value is even stranger. You have to set document.cookie to a value with an exact syntax:
```
document.cookie = "max_results=50; expires=Wed, 1 Jan 2020 20:53:47 UTC; path=/";
```
That statement looks like it would overwrite all other existing cookies, but (magically) it doesn’t!

A more ideal interface to setting a cookie would be something like:
```
set_cookie(name, value, days_to_expire);
```
Erasing a cookie is also unintuitive: you have to set the cookie to expire in the past. Instead, an ideal interface would be simply:
```
delete_cookie(name);
```
The lesson here is that you should never have to settle for an interface that’s less than ideal. You can always create your own wrapper functions to hide the ugly details of an interface you’re stuck with.

## Reshaping an Interface to Your Needs

A lot of code in a program is there just to support other code—for example, setting up inputs to a function or postprocessing the output. This “glue” code often has nothing to do with the real logic of your program. Mundane code like this is a great candidate to be pulled out into separate functions.

For example, let’s say you have a Python dictionary containing sensitive user information like { "username": "...", "password": "..." } and you need to put all that information into a URL. Because it’s sensitive, you decide to encrypt the dictionary first, using a Cipher class.

But Cipher expects a string of bytes as input, not a dictionary. And Cipher returns a string of bytes, but we need something that’s URL-safe. Cipher also takes a number of extra parameters and is pretty cumbersome to use.

What started as a simple task turns into a lot of glue code:
```
user_info = { "username": "...", "password": "..." }
user_str = json.dumps(user_info)
cipher = Cipher("aes_128_cbc", key=PRIVATE_KEY, init_vector=INIT_VECTOR, op=ENCODE)
encrypted_bytes = cipher.update(user_str)
encrypted_bytes += cipher.final() # flush out the current 128 bit block
url = "http://example.com/?user_info=" + base64.urlsafe_b64encode(encrypted_bytes)
...
```

Even though the problem we’re tackling is Encrypt the user’s information into a URL, the majority of this code is just doing Encrypt this Python object into a URL-friendly string. It’s easy to extract that subproblem:
```
def url_safe_encrypt(obj):
    obj_str = json.dumps(obj)
    cipher = Cipher("aes_128_cbc", key=PRIVATE_KEY, init_vector=INIT_VECTOR, op=ENCODE)
    encrypted_bytes = cipher.update(obj_str)
    encrypted_bytes += cipher.final() # flush out the current 128 bit block
    return base64.urlsafe_b64encode(encrypted_bytes)
```
Then, the resulting code to execute the real logic of the program is simple:
```
user_info = { "username": "...", "password": "..." }
url = "http://example.com/?user_info=" + url_safe_encrypt(user_info)
```

## Taking Things Too Far

As we said at the beginning of the chapter, our goal is to “aggressively identify and extract unrelated subproblems.” We say “aggressively” because most coders aren’t aggressive enough. But it’s possible to get overexcited and take things too far.

For example, the code from the previous section could have been broken down much further, like this:

```python=
def url_safe_encrypt_obj(obj):
    obj_str = json.dumps(obj)
    return url_safe_encrypt_str(obj_str)
    
def url_safe_encrypt_str(data):
    encrypted_bytes = encrypt(data)
    return base64.urlsafe_b64encode(encrypted_bytes)

def encrypt(data):
    cipher = make_cipher()
    encrypted_bytes = cipher.update(data)
    encrypted_bytes += cipher.final() # flush out any remaining bytes
    return encrypted_bytes

def make_cipher():
    return Cipher("aes_128_cbc", key=PRIVATE_KEY, init_vector=INIT_VECTOR, op=ENCODE)
```
Introducing all these tiny functions actually hurts readability, because the reader has more to keep track of, and following the path of execution requires jumping around.

There is a small (but tangible) readability cost of adding a new function to your code. In the previous case, nothing is being gained to offset this cost. It may make sense to add these smaller functions if they’re needed by other parts of your project. But until then, there is no need.
   
## Summary

A simple way to think about this chapter is to separate the generic code from the projectspecific code. As it turns out, most code is generic. By building a large set of libraries and helper functions to solve the general problems, what’s left will be a small core of what makes your program unique.

The main reason this technique helps is that it lets the programmer focus on smaller, welldefined problems that are detached from the rest of your project. As a result, the solutions to those subproblems tend to be more thorough and correct. You might also be able to reuse them later.

> FURTHER READING
> Martin Fowler’s Refactoring: Improving the Design of Existing Code (Fowler et al., Addison-Wesley Professional, 1999) describes the “Extract Method” of refactoring and catalogs many other ways to refactor your code.
> 
> Kent Beck’s Smalltalk Best Practice Patterns (Prentice Hall, 1996) describes the “Composed Method Pattern,” which lists a number of principles for breaking down your code into lots of little functions. In particular, one of the principles is “Keep all of the operations in a single method at the same level of abstraction.”
> 
>These ideas are similar to our advice of “extracting unrelated subproblems.” What we discussed in this chapter is a simple and particular case of when to extract a method.


























































