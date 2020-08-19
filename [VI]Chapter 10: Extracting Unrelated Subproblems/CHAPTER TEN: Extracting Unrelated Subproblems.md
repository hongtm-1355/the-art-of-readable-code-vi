# CHAPTER TEN: Extracting Unrelated Subproblems
# CHAPTER 10: Giải nén các vấn đề con
![](https://i.imgur.com/jVGgXnu.png)

Engineering is all about breaking down big problems into smaller ones and putting the solutions for those problems back together. Applying this principle to code makes it more robust and easier to read.
Các kĩ sư thường chia nhỏ những vấn đề lớn thành những vẫn để nhỏ hơn và để giải quyết các vấn đề đó. Áp dụng nguyên tắc này vào mã sẽ giúp mã trở lên mạnh mẽ hơn và dễ đọc hơn.

The advice for this chapter is to aggressively identify and extract unrelated subproblems. Here’s what we mean:
Lời khuyên cho chương này là tích cực xác định và chia nhỏ các vấn đề con không liên quan. Đây là ý của chúng tôi
    1. Look at a given function or block of code, and ask yourself, “What is the high-level goal of this code?”
    1. Nhìn vào một hàm hoặc một đoạn code, và tự hỏi bản thân, "Mục tiêu cao nhất của đoạn code này là gì ?"
    2. For each line of code, ask, “Is it working directly to that goal? Or is it solving an unrelated subproblem needed to meet it?”
    2. Với mỗi dòng code, hãy hỏi, "nó hoạt động đúng mục tiêu không ? hoặc nó có giải quyết vấn đề unrelated subproblem bạn cần khồn ?
    3. If enough lines are solving an unrelated subproblem, extract that code into a separate function.
    3. Nếu đủ số dòng để giải quyết vấn đề con không liên quan, hyax trích xuất mã đó ra một hàm riêng biệt

Extracting code into separate functions is something you probably do every day. But for this chapter, we decided to focus on the specific case of extracting unrelated subproblems, where the extracted code is blissfully unaware of why it’s being called.
Trích xuất mã thành những hàm riêng biệt là việc bạn có thể làm hàng ngày. Nhưng đối với chương nay,f chúng tôi quyết định tập trung vào trường hợp cụ thể của việc trích xuất các vấn đề con không liên quan, ./--------

As you’ll see, it’s an easy technique to apply but can improve your code substantially. Yet for some reason, many programmers don’t use this technique enough. The trick is to actively look for these unrelated subproblems.
Như bạn thấy, nó là một kiến thức dễ để áp dụng nhưng có thể cải thiện code của bạn đáng kể. Một vài lý do, nhiều lập tình viên không muốn sử dụng kỹ năng này đủ. Bí quyết là hãy chủ động tìm kiếm những đoạn mã con không liên quan

In this chapter, we will go through a variety of examples that illustrate this technique for different situations you might run into.
Trong chương này, chúng ta sẽ đi qua các ví dụ minh họa cho kỹ thuật cho các tình huống khác nhau mà bạn có thể gặp phải.

## Introductory Example: findClosestLocation()
## Ví dụ giới thieeuj: findClosestLocation()
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
Hầu hết đoạn code beentrong vòng lặp đnag giải quyết vấn đề không liên quan: tính toán khoảng cách hình cầu giữa 2 điểm vĩ độ. kinh đọ. Bới vì có rất nhiều mã, nên việc chia nó thành hàm bridge_distance() riêng biệt
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
Bây giờ thì nó sẽ trở thanh:
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
Mã này dễ đọc hơn nhiều vì người đọc có thể tập trung vào mục tiêu cấp cao mà không bị phân tâm bởi các phương trình hình học cường độ cao.

As an added bonus, spherical_distance() will be easier to test in isolation. And spherical_distance() is the type of function that could be reused in the future. This is why it’s an “unrelated” subproblem—it’s completely self-contained and unaware of how applications are using it.
Như một phần thưởng được thêm vào, Sphere_distance () sẽ dễ dàng kiểm tra hơn. Và bridge_distance () là loại hàm có thể được sử dụng lại trong tương lai. Đây là lý do tại sao nó là một vấn đề phụ “không liên quan” — nó hoàn toàn khép kín và không biết cách các ứng dụng đang sử dụng nó.
## Pure Utility Code

There is a core set of basic tasks that most programs do, such as manipulating strings, using hash tables, and reading/writing files.
Có một tập hợp cốt lõi của các tác vụ cơ bản mà hầu hết các chương trình đều thực hiện, chẳng hạn như thao tác chuỗi, sử dụng bảng băm và đọc / ghi tệp.

Often, these “basic utilities” are implemented by the built-in libraries in your programming language. For instance, if you want to read the entire contents of a file, in PHP you can call file_get_contents("filename") , or in Python, you can do open("filename").read() .
Thông thường, những “tiện ích cơ bản” này được thực hiện bởi các thư viện tích hợp trong ngôn ngữ lập trình của bạn. Ví dụ: nếu bạn muốn đọc toàn bộ nội dung của một tệp, trong PHP, bạn có thể gọi tệp_get_contents ("tên tệp"), hoặc trong Python, bạn có thể mở ("tên tệp"). Read ().

But sometimes you have to fill in the gaps yourself. In C++, for instance, there is no succinct way to read an entire file. Instead, you inevitably end up writing code like this:
Nhưng đôi khi bạn phải tự mình lấp đầy những khoảng trống. Ví dụ, trong C ++, không có cách nào ngắn gọn để đọc toàn bộ tệp. Thay vào đó, bạn chắc chắn phải viết mã như thế này:
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
Đây là một ví dụ cổ điển về vấn đề con không liên quan cần được trích xuất thành một hàm mới như ReadFileToString (). Bây giờ, phần còn lại của codebase của bạn có thể hoạt động như thể C ++ có hàm ReadFileToString ().

In general, if you find yourself thinking, “I wish our library had an XYZ() function,” go ahead and write it! (Assuming it doesn’t already exist.) Over time, you’ll build up a nice collection of utility code that can be used across projects.
Nói chung, nếu bạn thấy mình đang nghĩ, “Ước gì thư viện của chúng ta có hàm XYZ (),” hãy tiếp tục và viết nó! (Giả sử nó chưa tồn tại.) Theo thời gian, bạn sẽ xây dựng một bộ sưu tập mã tiện ích đẹp có thể được sử dụng trong các dự án.
xxx

## Other General-Purpose Code

When debugging JavaScript, programmers often use alert() to pop up a message box that displays some information to the programmer, the Web’s version of “ printf() debugging.” For example, the following function call submits data to the server using Ajax and then displays the dictionary returned from the server:
Khi debug JS, lập trình viên thường sử dụng `alert()` để bắn message và hiển thị thông tin tới lập trình viên. Đây là PHiên bản web of prinf(). Ví dụ, Ví dụ: lệnh gọi hàm sau gửi dữ liệu đến máy chủ bằng Ajax và sau đó hiển thị từ điển được trả về từ máy chủ:

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
Mục tiêu cao cả của đoạn code này là gọi Ajax tới sv, và xử lý response. Nhưng rất nhiều mã đang giải quyết vấn đề con không liên quan, Khá-in một từ điển. Thật dễ dàng để trích xuất mã đó thành một hàm như format_pretty (obj):
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
### Lợi ích bất ngờ
There are a lot of reasons why extracting format_pretty() is a good idea. It makes the calling code simpler, and format_pretty() is a handy function to have around.
Có rất nhiều lý do tại sao giải nén format_pretty () là một ý kiến hay. Nó làm cho mã gọi đơn giản hơn và format_pretty () là một hàm tiện dụng cần có.

But there’s another great reason that’s not as obvious: it’s easier to improve format_pretty() when the code is by itself. When you’re working on a smaller function in isolation, it feels easier to add features, improve reliability, take care of edge cases, and so on.
Nhưng có một lý do tuyệt vời khác không rõ ràng như vậy: việc cải thiện format_pretty () dễ dàng hơn khi mã là chính nó. Khi bạn đang làm việc trên một chức năng nhỏ hơn một cách riêng lẻ, bạn sẽ cảm thấy dễ dàng hơn khi thêm các tính năng, cải thiện độ tin cậy, xử lý các trường hợp cạnh, v.v.

Here are some cases format_pretty(obj) doesn’t handle:
• It expects obj to be an object. If instead it’s a plain string (or undefined ), the current code will throw an exception.
• It expects each value of obj to be a simple type. If instead it contains nested objects, the current code will display them as [object Object] , which isn’t very pretty.

Dưới đây là một số trường hợp format_pretty (obj) không xử lý:
• Nó mong đợi obj là một đối tượng. Nếu thay vào đó, đó là một chuỗi thuần túy (hoặc không xác định), mã hiện tại sẽ đưa ra một ngoại lệ.
• Nó mong đợi mỗi giá trị của obj là một kiểu đơn giản. Nếu thay vào đó, nó chứa các đối tượng lồng nhau, mã hiện tại sẽ hiển thị chúng dưới dạng [Đối tượng đối tượng], điều này không đẹp lắm.

Before we separated format_pretty() into its own function, it would have felt like a lot of work to make all these improvements. (In fact, recursively printing nested objects is very difficult without a separate function.)
Trước khi chúng ta tách `format_pretty()` thành hàm riêng, nó vẻ như rất nhiều điều phải làm để cả thiện tất cả. (Trên thực tế, việc in đệ quy các đối tượng lồng nhau là rất khó nếu không có một chức năng riêng biệt.)
But now, adding this functionality is easy. Here’s what the improved code looks like:
Nhưng bây giờ, việc thêm chức năng này rất dễ dàng. Đây là mã được cải tiến trông như thế nào:
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
Điều này bao gồm những thiếu sót được liệt kê trước đó và tạo ra đầu ra như thế này:
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
Các hàm ReadFileToString () và format_pretty () là những ví dụ tuyệt vời về các bài toán con không liên quan. Chúng rất cơ bản và có thể áp dụng rộng rãi nên chúng có khả năng được sử dụng lại trong các dự án. Các cơ sở mã thường có một thư mục đặc biệt cho mã như thế này (ví dụ: using /) để có thể dễ dàng chia sẻ.

General-purpose code is great because it’s completely decoupled from the rest of your project. Code like this is easier to develop, easier to test, and easier to understand. If only all of your code could be like this!
Mã mục đích chung rất tuyệt vì nó được tách biệt hoàn toàn khỏi phần còn lại của dự án của bạn. Mã như thế này dễ phát triển hơn, dễ kiểm tra hơn và dễ hiểu hơn. Giá như tất cả mã của bạn có thể như thế này!

Think about many of the powerful libraries and systems that you use, such as SQL databases, JavaScript libraries, and HTML templating systems. You don’t have to worry about their internals—those codebases are completely isolated from your project. As a result, your project’s codebase remains small.
Hãy nghĩ về nhiều thư viện và hệ thống mạnh mẽ mà bạn sử dụng, chẳng hạn như cơ sở dữ liệu SQL, thư viện JavaScript và hệ thống tạo khuôn mẫu HTML. Bạn không phải lo lắng về nội bộ của họ — những cơ sở mã đó hoàn toàn cách biệt với dự án của bạn. Do đó, cơ sở mã của dự án của bạn vẫn còn nhỏ.

The more of your project you can break away as isolated libraries, the better, because the rest of your code will be smaller and easier to think about.
Bạn càng có thể chia nhỏ dự án thành các thư viện biệt lập thì càng tốt, vì phần còn lại của mã của bạn sẽ nhỏ hơn và dễ nghĩ hơn.

> IS THIS TOP-DOWN OR BOTTOM-UP PROGRAMMING?
>
>Top-down programming is a style where the highest-level modules and functions are designed first and the lower-level functions are implemented as needed to support them.

>Bottom-up programming tries to anticipate and solve all the subproblems first and then build the higher-level components using these pieces.

>This chapter isn’t advocating one method over the other. Most programming involves a combination of both. What’s important is the end result: subproblems are removed and tackled separately.

> ĐÂY LÀ LẬP TRÌNH XUỐNG TRÊN HAY ĐÁP ÁN?
>
> Lập trình từ trên xuống là kiểu mà các mô-đun và chức năng cấp cao nhất được thiết kế trước tiên và các chức năng cấp thấp hơn được triển khai khi cần thiết để hỗ trợ chúng.

> Lập trình từ dưới lên cố gắng dự đoán và giải quyết tất cả các vấn đề con trước, sau đó xây dựng các thành phần cấp cao hơn bằng cách sử dụng các phần này.

> Chương này không ủng hộ phương pháp này hơn phương pháp kia. Hầu hết các lập trình liên quan đến sự kết hợp của cả hai. Điều quan trọng là kết quả cuối cùng: các vấn đề con được loại bỏ và giải quyết riêng.
## Project-Specific Functionality
Ideally, the subproblems you extract would be completely project-agnostic. But even if they’re not, that’s okay. Breaking off subproblems still works wonders.
Lý tưởng nhất là các bài toán con mà bạn trích xuất sẽ hoàn toàn không theo dự án. Nhưng ngay cả khi họ không, điều đó vẫn ổn. Việc phá vỡ các vấn đề con vẫn làm việc kỳ diệu.

Here is an example from a business reviews website. This Python code creates a new Business object and sets its name, url, and date_created:
Đây là một ví dụ từ một trang web đánh giá doanh nghiệp. Mã Python này tạo một đối tượng Business mới và đặt tên, url và date_create của nó:
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
Url phải là phiên bản "sạch" của tên. Ví dụ: nếu tên là “A.C. Joe’s Tire & Smog, Inc., ”url sẽ là“ / biz / ac-joes-lốp-Smog-inc ”.

The unrelated subproblem in this code is: Turn a name into a valid URL. We can extract this code quite easily. While we’re at it, we can also precompile the regular expressions (and give them readable names):
Vấn đề con không liên quan trong đoạn mã này là: Biến tên thành một URL hợp lệ. Chúng tôi có thể trích xuất mã này khá dễ dàng. Trong khi thực hiện, chúng tôi cũng có thể biên dịch trước các biểu thức chính quy (và đặt tên cho chúng có thể đọc được):
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
Bây giờ mã gốc có một mẫu "thông thường" hơn nhiều:

```
business = Business()
business.name = request.POST["name"]
business.url = "/biz/" + make_url_friendly(business.name)
business.date_created = datetime.datetime.utcnow()
business.save_to_database()
```
This code requires far less effort to read because you aren’t distracted by the regular expressions and deep string manipulation.
Mã này đòi hỏi ít nỗ lực hơn để đọc vì bạn không bị phân tâm bởi các biểu thức chính quy và thao tác chuỗi sâu.

Where should you put the code for make_url_friendly()? It seems like a fairly general function, so it might make sense to put it in a separate util/ directory. On the other hand, those regular expressions were designed with U.S. business names in mind, so perhaps the code should stay in the same file where it’s used. It actually doesn’t matter that much, and you can easily move the definition later on. What’s more important is that make_url_friendly() was extracted at all.
Bạn nên đặt mã cho make_url_friendly () ở đâu? Nó có vẻ như là một hàm khá chung chung, vì vậy có thể hợp lý nếu đặt nó trong một thư mục use / riêng biệt. Mặt khác, các cụm từ thông dụng đó được thiết kế với các tên doanh nghiệp của Hoa Kỳ, vì vậy, có lẽ mã nên ở trong cùng một tệp nơi nó được sử dụng. Nó thực sự không quan trọng lắm và bạn có thể dễ dàng di chuyển định nghĩa sau này. Điều quan trọng hơn là make_url_friendly () đã được trích xuất.

## Simplifying an Existing Interface
Everybody loves when a library offers a clean interface—one that takes few arguments, doesn’t need much setup, and generally requires little effort to use. It makes your code look elegant: simple and powerful at the same time.
Mọi người đều thích khi một thư viện cung cấp một giao diện rõ ràng — một giao diện cần ít đối số, không cần thiết lập nhiều và thường chỉ cần ít nỗ lực để sử dụng. Nó làm cho mã của bạn trông thanh lịch: đơn giản và mạnh mẽ cùng một lúc.

But if an interface you’re using isn’t clean, you can still make your own “wrapper” functions that are.
Nhưng nếu giao diện bạn đang sử dụng không sạch, bạn vẫn có thể tạo các chức năng “trình bao bọc” của riêng mình.

For example, dealing with browser cookies in JavaScript is far from ideal. Conceptually, cookies are a set of name/value pairs. But the interface the browser provides presents a single document.cookie string whose syntax is:
Ví dụ, xử lý cookie của trình duyệt trong JavaScript không phải là lý tưởng. Về mặt khái niệm, cookie là một tập hợp các cặp tên / giá trị. Nhưng giao diện mà trình duyệt cung cấp trình bày một chuỗi document.cookie duy nhất có cú pháp là:
```
name1=value1; name2=value2; ...
```
To find the cookie you want, you’re forced to parse this giant string yourself. Here’s an example of code that reads the value for the cookie named "max_results":
Để tìm cookie bạn muốn, bạn buộc phải tự phân tích cú pháp chuỗi khổng lồ này. Dưới đây là một ví dụ về mã đọc giá trị cho cookie có tên "max_results":
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
Chà, đó là một đoạn mã xấu xí. Rõ ràng, có một hàm get_cookie () đang chờ được tạo để chúng ta có thể viết:
```
var max_results = Number(get_cookie("max_results"));
```
Creating or changing a cookie value is even stranger. You have to set document.cookie to a value with an exact syntax:
Việc tạo hoặc thay đổi giá trị cookie thậm chí còn xa lạ. Bạn phải đặt document.cookie thành một giá trị với cú pháp chính xác:
```
document.cookie = "max_results=50; expires=Wed, 1 Jan 2020 20:53:47 UTC; path=/";
```
That statement looks like it would overwrite all other existing cookies, but (magically) it doesn’t!

A more ideal interface to setting a cookie would be something like:
Câu lệnh đó có vẻ như nó sẽ ghi đè lên tất cả các cookie hiện có khác, nhưng (kỳ diệu) thì không!

Một giao diện lý tưởng hơn để đặt cookie sẽ giống như:
```
set_cookie(name, value, days_to_expire);
```
Erasing a cookie is also unintuitive: you have to set the cookie to expire in the past. Instead, an ideal interface would be simply:
Xóa cookie cũng không trực quan: bạn phải đặt cookie hết hạn trong quá khứ. Thay vào đó, một giao diện lý tưởng sẽ đơn giản là:
```
delete_cookie(name);
```
The lesson here is that you should never have to settle for an interface that’s less than ideal. You can always create your own wrapper functions to hide the ugly details of an interface you’re stuck with.
Bài học ở đây là bạn không bao giờ phải tìm một giao diện kém lý tưởng. Bạn luôn có thể tạo các chức năng trình bao bọc của riêng mình để ẩn các chi tiết xấu xí của giao diện mà bạn gặp khó khăn.

## Reshaping an Interface to Your Needs

A lot of code in a program is there just to support other code—for example, setting up inputs to a function or postprocessing the output. This “glue” code often has nothing to do with the real logic of your program. Mundane code like this is a great candidate to be pulled out into separate functions.
Có rất nhiều mã trong một chương trình chỉ để hỗ trợ mã khác — ví dụ: thiết lập đầu vào cho một hàm hoặc xử lý sau đầu ra. Mã "keo" này thường không liên quan gì đến logic thực của chương trình của bạn. Mã Mundane như thế này là một ứng cử viên tuyệt vời để được rút ra thành các chức năng riêng biệt.

For example, let’s say you have a Python dictionary containing sensitive user information like { "username": "...", "password": "..." } and you need to put all that information into a URL. Because it’s sensitive, you decide to encrypt the dictionary first, using a Cipher class.
Ví dụ: giả sử bạn có từ điển Python chứa thông tin người dùng nhạy cảm như {"tên người dùng": "...", "mật khẩu": "..."} và bạn cần đưa tất cả thông tin đó vào một URL. Vì nó nhạy cảm nên trước tiên bạn quyết định mã hóa từ điển bằng cách sử dụng lớp Mật mã.

But Cipher expects a string of bytes as input, not a dictionary. And Cipher returns a string of bytes, but we need something that’s URL-safe. Cipher also takes a number of extra parameters and is pretty cumbersome to use.
Nhưng Cipher mong đợi một chuỗi byte làm đầu vào, không phải từ điển. Và Cipher trả về một chuỗi byte, nhưng chúng tôi cần thứ gì đó an toàn cho URL. Cipher cũng có một số tham số bổ sung và sử dụng khá cồng kềnh.

What started as a simple task turns into a lot of glue code:
Những gì bắt đầu như một nhiệm vụ đơn giản biến thành rất nhiều mã keo:
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
Mặc dù vấn đề chúng tôi đang giải quyết là Mã hóa thông tin của người dùng thành một URL, phần lớn mã này chỉ thực hiện Mã hóa đối tượng Python này thành một chuỗi thân thiện với URL. Thật dễ dàng để giải nén vấn đề con đó:
```
def url_safe_encrypt(obj):
    obj_str = json.dumps(obj)
    cipher = Cipher("aes_128_cbc", key=PRIVATE_KEY, init_vector=INIT_VECTOR, op=ENCODE)
    encrypted_bytes = cipher.update(obj_str)
    encrypted_bytes += cipher.final() # flush out the current 128 bit block
    return base64.urlsafe_b64encode(encrypted_bytes)
```
Then, the resulting code to execute the real logic of the program is simple:
Sau đó, mã kết quả để thực thi logic thực của chương trình rất đơn giản:
```
user_info = { "username": "...", "password": "..." }
url = "http://example.com/?user_info=" + url_safe_encrypt(user_info)
```

## Taking Things Too Far

As we said at the beginning of the chapter, our goal is to “aggressively identify and extract unrelated subproblems.” We say “aggressively” because most coders aren’t aggressive enough. But it’s possible to get overexcited and take things too far.
Như chúng tôi đã nói ở đầu chương, mục tiêu của chúng tôi là “xác định rõ ràng và trích xuất các vấn đề con không liên quan”. Chúng tôi nói "tích cực" bởi vì hầu hết các lập trình viên không đủ năng nổ. Nhưng bạn có thể quá phấn khích và đưa mọi thứ đi quá xa.

For example, the code from the previous section could have been broken down much further, like this:
Ví dụ: mã từ phần trước có thể đã được chia nhỏ hơn nhiều, như thế này:
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
Việc giới thiệu tất cả các chức năng nhỏ bé này thực sự làm ảnh hưởng đến khả năng đọc, vì người đọc có nhiều thứ để theo dõi hơn và việc theo dõi quá trình thực thi đòi hỏi phải nhảy xung quanh.

There is a small (but tangible) readability cost of adding a new function to your code. In the previous case, nothing is being gained to offset this cost. It may make sense to add these smaller functions if they’re needed by other parts of your project. But until then, there is no need.
Có một chi phí nhỏ (nhưng hữu hình) về khả năng đọc của việc thêm một chức năng mới vào mã của bạn. Trong trường hợp trước, không thu được gì để bù đắp chi phí này. Có thể hợp lý khi thêm các chức năng nhỏ hơn này nếu chúng được các phần khác trong dự án của bạn cần. Nhưng cho đến lúc đó thì không cần nữa.
## Summary

A simple way to think about this chapter is to separate the generic code from the projectspecific code. As it turns out, most code is generic. By building a large set of libraries and helper functions to solve the general problems, what’s left will be a small core of what makes your program unique.
Một cách đơn giản để nghĩ về chương này là tách mã chung khỏi mã cụ thể của dự án. Hóa ra, hầu hết mã là chung chung. Bằng cách xây dựng một bộ thư viện lớn và các chức năng trợ giúp để giải quyết các vấn đề chung, những gì còn lại sẽ là phần cốt lõi nhỏ làm cho chương trình của bạn trở nên độc đáo.

The main reason this technique helps is that it lets the programmer focus on smaller, welldefined problems that are detached from the rest of your project. As a result, the solutions to those subproblems tend to be more thorough and correct. You might also be able to reuse them later.
Lý do chính mà kỹ thuật này hữu ích là nó cho phép lập trình viên tập trung vào các vấn đề nhỏ hơn, được xác định rõ ràng, tách rời khỏi phần còn lại của dự án của bạn. Kết quả là, các giải pháp cho các vấn đề con đó có xu hướng triệt để và chính xác hơn. Bạn cũng có thể sử dụng lại chúng sau này.

> FURTHER READING
> Martin Fowler’s Refactoring: Improving the Design of Existing Code (Fowler et al., Addison-Wesley Professional, 1999) describes the “Extract Method” of refactoring and catalogs many other ways to refactor your code.
>
> Kent Beck’s Smalltalk Best Practice Patterns (Prentice Hall, 1996) describes the “Composed Method Pattern,” which lists a number of principles for breaking down your code into lots of little functions. In particular, one of the principles is “Keep all of the operations in a single method at the same level of abstraction.”
>
>These ideas are similar to our advice of “extracting unrelated subproblems.” What we discussed in this chapter is a simple and particular case of when to extract a method.

> ĐỌC THÊM
> Martin Fowler’s Refactoring: Cải thiện thiết kế của mã hiện có (Fowler et al., Addison-Wesley Professional, 1999) mô tả “Phương pháp trích xuất” của việc tái cấu trúc và liệt kê nhiều cách khác để cấu trúc lại mã của bạn.
>
> Các Mẫu Thực hành Tốt nhất dành cho Smalltalk của Kent Beck (Prentice Hall, 1996) mô tả “Mẫu Phương pháp Tổng hợp”, liệt kê một số nguyên tắc để chia nhỏ mã của bạn thành nhiều chức năng nhỏ. Đặc biệt, một trong những nguyên tắc là "Giữ tất cả các hoạt động trong một phương pháp duy nhất ở cùng một mức độ trừu tượng."
>
> Những ý tưởng này tương tự như lời khuyên của chúng tôi về việc "trích xuất các vấn đề con không liên quan." Những gì chúng ta đã thảo luận trong chương này là một trường hợp đơn giản và cụ thể về thời điểm trích xuất một phương thức.
