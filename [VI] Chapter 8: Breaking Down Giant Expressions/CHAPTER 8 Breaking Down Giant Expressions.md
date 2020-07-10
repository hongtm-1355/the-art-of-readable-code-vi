# CHAPTER 8 Breaking Down Giant Expressions
# Breaking Down Giant Expressions

The giant squid is an amazing and intelligent animal, but its near-perfect body design has one fatal flaw: it has a donut-shaped brain that wraps around its esophagus. So if it swallows too much food at once, it gets brain damage.

Mực khổng lồ là loài động vật tuyệt vời và thông minh, nhưng thiết kế cơ thể nó lại lại có một lỗ hổng chết người: nó có một bộ não quấn quanh thực quản của nó nên nếu nó nuốt quá nhiều thức ăn, não của nó có thể bị tổn thương

What does this have to do with code? Well, code that comes in “chunks” that are too big can have the same kind of effect. Recent research suggests that most of us can only think about three or four “things” at a time.* Simply put, the larger an expression of code is, the harder it will be to understand.

Điều đó thì liên quan gì tới code ? ....... Nghiên cứu gần đây cho rằng hầu hết chúng ta nghĩ về điều đó ba tới bốn lần cùng một thời gian. Nói cách đơn giản, một biểu thức code lớn thì sẽ gây khó hiểu.

> KEY IDEA
> Break down your giant expressions into more digestible pieces.
> Chia nhỏ biểu thức của thành nhiều phần để dễ tiêu hóa hơn.
> 

In this chapter, we'll go throught various ways you can manipulate and break down your code so that it's easiers to swallow

Trong chương này, chúng ta sẽ đi qua những cách thú vị để bạn có thể vận dụng và chia nhỏ code một cách dễ dàng.

## Explaining Variables - Diễn giải biến

The simplest way to break down an expression is to introduce an extra variable that captures a smaller subexpression. This extra variable is sometimes called an "explaining variable" because it helps explain what the subexpression means.
Cách đơn giản nhất để chia nhỏ một biểu thức là đưa nó về một biến phụ của một biểu thức nhỏ hơn. Phiến vụ là cách gọi của "explainin variable" bởi vì nó giúp giải thích ý nghĩa mà biển thức con.

Here is an example:
Đây là một ví dụ:
```python
if line.split(':')[0].strip() == "root":
    ...
```
Here is the same code, now with an explaining variable:
Đây là code ví dụ, khi đã explaining varaible:
```python=
username = line.split(':')[0].strip()
if username == "root":
    ...
```

## Summay Variables - Tổng hợp biến

Even if an expression doesn’t need explaining (because you can figure out what it means), it can still be useful to capture that expression in a new variable. We call this a summary variable if its purpose is simply to replace a larger chunk of code with a smaller name that can be managed and thought about more easily.

Thâm chí nếu một biểu thức không cần phải explaining (bởi vì bạn có thể tìm ra ý nghĩa của nó), thì vẫn rất hữu ích để thể hiện lại biểu thức đó trong một biến mới. Chúng ta gọi điều đó là summary variablke nếu mục địch chỉ đơn giản là thay thế đoạn mã lớn với một cái tên nhỏ hơn mà có thể quản lý và suy nghĩ dễ dàng hơn.

For example, consider the expressions in this code:
Ví dụ, hãy xem xét biểu thức sau:
```js
if (request.user.id == document.owner_id) {
    // user can edit this document...
}

if (request.user.id != document.owner_id) {
    // document is read-only...
}
```

The expression `request.user.id == document.owner_id` may not seem that big, but it has five variables, so it takes a little extra time to think about.
Biểu thức `request.user.id == document.owner_id` nhìn có vẻ không lớn lắm, nhưng nó có 5 biến, nó khiến ra tốn nhiều thời gian để nghĩ về nó

The main concept in this code is, “Does the user own the document?” That concept can be stated more clearly by adding a summary variable:
Ý tưởng chính trong code là, "User có phải chủ sở hữu của tài liệu không ?". Concept có thể được phát biểu rõ ràng hơn bằng cách thêm 1 biến tổng hợp (summary variable):

```js
final boolean user_owns_document = (request.user.id == document.owner_id);
if (user_owns_document) {
    // user can edit this document...
}
...
if (!user_owns_document) {
    // document is read-only...
}
```
It may not seem like much, but the statement `if (user_owns_document)` is a little easier to think about. Also, having `user_owns_document` defined at the top tells the reader upfront that “this is a concept we’ll be referring to throughout this function.”
Có vẻ như không nhiều, nhưng câu lệnh `if (user_owns_document)` có chút gì đó dễ để nghĩ hơn. Ngoải ra, việc định nghĩa user_owns_document ở đầu nói cho người đọc biết trước rằng "đây là một khái niệm chung ta sẽ đề cập nó trong suốt function này".

## Using De Morgan’s Laws - Sử dụng luật De Morgan’s

If you ever took a course in circuits or logic, you might remember De Morgan’s laws. They are two ways to rewrite a boolean expression into an equivalent one.
Nếu bạn đã từng tham gia một khóa học về mạch điện hoặc logic, bạn sẽ nhớ tới Quy tắc De Morgan's. Có hai cách để viết một biểu thức boolean thành một biểu thức tương đương:
```
1) not (a or b or c) ⇔ (not a) and (not b) and (not c)
2) not (a and b and c) ⇔ (not a) or (not b) or (not c)
```

If you have trouble remembering these laws, a simple summary is “Distribute the not and switch and/or.” (Or going the other way, you “factor out the not.”)
Nếu bạn gặp vấn đề khi cố nhớ quy luật này, một cách tóm tắt đơn giản là "Not của các or/and = and/or của các not"

You can sometimes use there laws to make a boolean expression more readable. For instance, if your code is:
Bạn thi thoảng có thể sử dụng quy luật này để làm cho biểu thức boolean dễ đọc hơn. Ví dụ, nếu code của bạn như này:
```
if (!(file_exists && !is_protected)) Error("Sorry, could not read file.");
```
It can be rewritten to:
Nó có thể được viết lại thành:

```
if (!file_exists || is_protected) Error("Sorry, could not read file.");
```


## Abusing Short-Circuit Logic - Lạm dụng Mạch Logic ngắn

In most programming languages, boolean operators perform short-circuit evaluation. For example, the statement `if (a || b)` won’t evaluate b if a is true. This behavior is very handy but can sometimes be abused to accomplish complex logic.
Hầu hết các ngôn ngữ lập trình, biểu thức boolean được biểu diễn ở dạng biểu thức ngắn. Ví dụ, `if (a || b)` không là `b` nếu `a` true. Hành vi này khá tiện dụng nhưng đôi khi có thể khiến cho code phức tạp hơn.

Here is an example of a statement once written by one of the authors:
Đây là một ví dụ của một câu được biết với vài trong những tác giả:
```
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

In English, what this code is saying is, “Get the bucket for this key. If the bucket is not null, then make sure it isn’t occupied.”
Trong tiếng anh, thứ mà đoạn code kia nói là "Lấy một bucket ứng với key này. Nếu bucket không null thì hãy dùng nó"

Even though it’s only one line of code, it really makes most programmers stop and think. Now compare it to this code:
Cả khi nó chỉ có một dòng code, nó cũng dễ khiến lập trình viên dừng lại và nghĩ. Bây giờ hãy so sánh nó với code bên dưới.
```
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```

It does exactly the same thing, and even though it’s two lines of code, it’s much easier to understand.
Nó thực hiện chính xác những điều bên trên, cả khi nó tốn hơn hai dòng, nhưng nó sẽ dễ hiểu hơn.

So why was the code written as a single giant expression in the first place? At the time, it felt very clever. There’s a certain pleasure in paring logic down to a concise nugget of code. That’s understandable—it’s like solving a miniature puzzle, and we all like to have fun at work. The problem is that the code was a mental speed bump for anyone reading through the code.

Vậy tại sao code lại được viết như ở lúc đầu ? Ở thời điểm đó, ta cảm thấy rất thông minh. Có một niềm vui nhất định trong việc so sánh logic với đoạn mã ngắn. Điều đó có thể hiểu được - nó giống như giải một câu đố, và tất cả chúng ta đều rất vui với việc đó. Vấn đề là đoạn code là một cú hích thẳng vào tâm trí bất cứ ai đọc qua nó.

> KEY IDEA
> Beware of “clever” nuggets of code—they’re often confusing when others read the code later.
> Hãy cẩn thận với những đoạn code kiểu quá thông minh như vậy - họ thường khó hiểu khi đọc lại sau nó.
> 

Does this mean you should avoid making use of short-circuit behavior? No. There are plenty of cases where it can be used cleanly, for instance:
Điều này nghĩ là bạn nên tránh sử dụng những hành vi short-ciruit? Không, trong một vài trường hợp, sử dụng sẽ giúp code tốt hơn, ví dụ:

```
if (object && object->method()) ...
```

There is also a newer idiom worth mentioning: in languages like Python, JavaScript, and Ruby, the “or” operator returns one of its arguments (it doesn’t convert to a boolean), so code like:
Ngoài ra còn một thành ngữ đáng được nói: trong ngôn ngữ như Python, JS, Ruby, toán tử "or" trả về trong đối số của nó (nó không convert sang boolean)
```
x = a || b || c
```
can be used to pick out the first “truthy” value from a, b, or c.
có thể được sử dụng để chọn giá trị tồn tại.

## Example: Wrestling with Complicated Logic - Thách đầu với logic phức tạp

Suppose you’re implementing the following Range class:
Giả sử bạn đang viết một class Range như sau:

```
struct Range {
    int begin;
    int end;
    // For example, [0,5) overlaps with [3,8)
    bool OverlapsWith(Range other);
};
```

The following figure shows some example ranges:
Hình bên dưới cho bạn viết chi tiết hơn:

![](https://i.imgur.com/dl8BxoG.png)

Note that end is noninclusive. So A, B, and C don’t overlap with each other, but D overlaps with all of them
Chú ý rằng, cuối của khoảng không được bao gồm. A, B, và C không chồng chéo từng đôi một, nhưng D lại trồng trên rất cả.

Here is one attempt at implementing OverlapsWith()—it checks if either endpoint of its range falls inside the other’s range:
Ở đây có một có một hàm `OverlapsWith()` - nó kiểm tra xem điểm cuối của range có nằm trong range khác không
```
bool Range::OverlapsWith(Range other) {
    // Check if 'begin' or 'end' falls inside 'other'.
    return (begin >= other.begin && begin <= other.end) ||
    (end >= other.begin && end <= other.end);
}
```

Even though the code is only two lines long, there’s a lot going on. The following figure shows all the logic involved.
Mặc dù đoạn code kia chỉ 2 dòng, nhưng rất nhiều điều xảy ra trong đó. Hình dưới đây cho ta thấy các logic liên quan.

![](https://i.imgur.com/LffCprh.png)


There are so many cases and conditions to think about that it’s easy for a bug to slip by.
Có khá nhiều trường hợp và so sánh để nghĩ rằng nó sẽ có rất nhiều bug.

Speaking of which, there is a bug. The previous code will claim that the Range `[0,2)` overlaps with `[2,4)` when in fact it doesn’t.
Nói về điều đó, có một bug. Ở đoạn code trước sẽ báo là Range `[0, 2)` trồng với `[2, 4)` nhưng thực tế thì không.

The problem is that you have to be careful when comparing begin/end values using <= or just <. Here’s a fix to this problem:
Vấn đề là bạn hãy thận trọng khi so sánh giá trị begin/end sử dụng `<=` hoặc `<`. Đây là cách fix cho vấn đề trên:

```
return (begin >= other.begin && begin < other.end) ||
        (end > other.begin && end <= other.end);
```

Now it’s correct, right? Actually, there’s another bug. This code has ignored the case when begin/end completely surround other.
Bây giờ, nó đã chính xác hoàn toản ?. Thực sự thì, có một bug khác. Đoạn code này bỏ qua trường hợp begin/end của other range bọc range.

Here's a fix:
Đây là đoạn sửa lỗi:

```
return (begin >= other.begin && begin < other.end) ||
    (end > other.begin && end <= other.end) ||
    (begin <= other.begin && end >= other.end);
```
Yikes—this code has become way too complicated. You can’t expect anyone to read this code and confidently know that it’s correct. So what do we do? How can we break down this giant expression?
Yikes- đoạn code đã trở lên phức tạp. Bạn không thể yêu cầu ai đó đọc đoạn code này và tự tien rằng nó đúng. Vậy ta phải làm gì ? Chúng ta breakdown nó như thế nào ?

### Finding a More Elegant Approach - Tìm một cách tiếp cận khéo léo

This is one of those times when you should stop and consider a different approach altogether. What started as a simple problem (checking whether two ranges overlap) turned into a surprisingly convoluted piece of logic. This is often a sign that there must be an easier way.
Đây là một trong những thời điểm mà bạn nên dừng và xem xét một cách tiếp cận khác. Điều được bắt đầu cho vấn đề đơn giản (kiểm tra 2 range đè nhau) đã biến thành một logic phực tạp hơn nhiều. Đây thường là dấu hiệu cho thấy phải có cách dễ dàng hơn.

But finding a more elegant solution takes creativity. How do you go about it? One technique is to see if you can solve the problem the “opposite” way. Depending on the situation you’re in, this could mean iterating through arrays in reverse or filling in some data structure backward rather than forward.
Nhưng tìm một giải pháp tiếp cận khác cần sự sáng tạo. Bạn làm thế nào để đi đúng ? Một kỹ thuật để xem liệu nếu bạn có thể xử lý vấn đề theo cách "opposite-đối lập". Tùy thuộc vào tính huống bạn gặp phải, điều này có thể nghãi là lặp một mảng theo chiều ngược lại hoặc điền một cấu trúc dữ liệu khác.

Here, the opposite of OverlapsWith() is “doesn’t overlap.” Determining if two ranges don’t overlap turns out to be a much simpler problem, because there are only two possibilities:
Ở đây, opposite của `OverlapsWith()` là "không đè". Xác định nếu hai vùng không đè nên nhau sẽ đơn giản hơn, bởi vì chỉ có hai khả năng:
1. Kết thúc của vùng khác trước bắt đầu vùng xét.
1. The other range ends before this one begins.

2. Bắt đầu của vùng khác sau kết thúc vùng xét. 
2. The other range begins after this one ends.


We can turn this into code quite easily:
Chúng ta có thể xem đoạn code bên dưới.

```
bool Range::OverlapsWith(Range other) {
    if (other.end <= begin) return false; // They end before we begin
    if (other.begin >= end) return false; // They begin after we end
    return true; // Only possibility left: they overlap
}
```
Each line of code here is much simpler—it involves only a single comparison. That leaves the reader with enough brainpower to focus on whether <= is correct.
Mỗi dòng code dưới đây trông đơn giản hơn, nó chie liên quan tới một so sánh duy nhất.

## Breaking Down Giant Statements - Phá vỡ Statements

This chapter is about breaking down individual expressions, but the same techniques apply to breaking down larger statements as well. For example, the following JavaScript code has a lot to take in at once:
Chương này nói về vấn đề chia nhỏ một biểu thức, nhưng công nghệ được áp dụng để chia nhỏ statements to thì giống nhau. Ví dụ: JS sau đây có nhiều thứ để làm cùng lúc:

```javascript=
var update_highlight = function (message_num) {
    if ($("#vote_value" + message_num).html() === "Up") {
        $("#thumbs_up" + message_num).addClass("highlighted");
        $("#thumbs_down" + message_num).removeClass("highlighted");
    } else if ($("#vote_value" + message_num).html() === "Down") {
        $("#thumbs_up" + message_num).removeClass("highlighted");
        $("#thumbs_down" + message_num).addClass("highlighted");
    } else {
        $("#thumbs_up" + message_num).removeClass("highighted");
        $("#thumbs_down" + message_num).removeClass("highlighted");
    }
};
```
The individual expressions in this code aren’t that big, but when placed all together, it forms one giant statement that hits you all at once.
Chia biểu thức bên trong đoạn code không lớn, nhưng khi đặt chúng cạnh nhau, nó tạo thành một statement mà đánh vào tâm lý bạn cùng lúc rất nhiều.

Fortunately, a lot of the expressions are the same, which means we can extract them out as summary variables at the top of the function (this is also an instance of the DRY—Don’t Repeat Yourself—principle):
May mắn thay, những biểu thức như vậy thường giống nhau, tức là chúng ta có thể trích xuất chúng dưới dạng summary variable ở top của function(Đây là là ví dụ của nguyễn tắc DRY)

```javascript
var update_highlight = function(message_num) {
    var thumbs_up = $("#thumbs_up" + message_num);
    var thumbs_down = $("#thumbs_down" + message_num);
    var vote_value = $("#vote_value" + message_num).html();
    var hi = "highlighted";
    if (vote_value === "Up") {
        thumbs_up.addClass(hi);
        thumbs_down.removeClass(hi);
    } else if (vote_value === "Down") {
        thumbs_up.removeClass(hi);
        thumbs_down.addClass(hi);
    } else {
        thumbs_up.removeClass(hi);
        thumbs_down.removeClass(hi);
    }
};
```
The creation of ` var hi = "highlighted"` isn’t strictly needed, but as there were six copies of it, there were compelling benefits:
Sự sáng tạo của `hi = "highlighted"` không cần thiết, nhưng có tận 6 bản sao của nó, và nó óc những lợi ích nhất định: 

* It helps avoid typing mistakes. (In fact, did you notice that in the first example, the string was misspelled as "highighted" in the fifth case?)
* Nó chúng ta tránh được những nhầm lẫn trong việc nhập liệu. (Sự thật, bạn có chú ý rằng trong ví dụ đầu tiên, chúng ta đã sai chính tả "highighted")

* It shrinks the line width even more, making the code easier to scan through.
* Nó thu hẹp chiều rộng dòng hơn, khiến code dễ dàng quét hơn.

* If the class name needed to change, there’s just one place to change it.
* Nếu class name cần được thay đối, thì chỉ có một chỗ để thay đổi.

## Another Creative Way to Simplify Expressions - Một cách sáng tạo khác để đơn giản hóa biểu thức

Here’s another example with a lot going on in each expression, this time in C++:
Đây là một ví dụ khác biểu diễn một hiểu thức trong C++:

```
void AddStats(const Stats& add_from, Stats* add_to) {
    add_to->set_total_memory(add_from.total_memory() + add_to->total_memory());
    add_to->set_free_memory(add_from.free_memory() + add_to->free_memory());
    add_to->set_swap_memory(add_from.swap_memory() + add_to->swap_memory());
    add_to->set_status_string(add_from.status_string() + add_to->status_string());
    add_to->set_num_processes(add_from.num_processes() + add_to-    >num_processes());
...
}
```

Once again, your eyes are faced with code that’s long and similar, but not exactly the same. After ten seconds of careful scrutiny, you might realize that each line is doing the same thing, just to a different field each time:
Một lần nữ, con mắt của bạn sẽ phải đối mắt với code rất dài, nhưng không chia nhỏ như cũ. Sau 10s tập chung, bạn có lẽ nhận ra rằng mỗi dòng đang cùng làm một việc, chỉ khác mỗi vị trí:
```
add_to->set_XXX(add_from.XXX() + add_to->XXX());
```
In C++, we can define a macro to implement this:
Trong C++, chúng ta có để định nghĩa một macro cho điều đó:

```
void AddStats(const Stats & add_from, Stats * add_to) {
    #
    define ADD_FIELD(field) add_to - > set_## field(add_from.field() + add_to - > field())
    ADD_FIELD(total_memory);
    ADD_FIELD(free_memory);
    ADD_FIELD(swap_memory);
    ADD_FIELD(status_string);
    ADD_FIELD(num_processes);
    ...#undef ADD_FIELD
}
```

Now that we’ve stripped away all the clutter, you can look at the code and immediately understand the essence of what’s happening. It’s very clear that each line is doing the same thing.
Bây giờ, chúng ta đã loại bỏ tất cả sự lộn xộn, bạn có thể nhìn lại code và ngay lập tức hiểu được bản chất của nó. Nó đã sạch hơn.

Note that we’re not advocating using macros very often—in fact, we usually avoid them because they can make code confusing and introduce subtle bugs. But sometimes, as in this case, they’re simple and can provide a clear benefit to readability.
Chú ý rằng chúng ta không ủng hộ việc sử dụng macro thừng xuyên, sự thật, chúng ta thường tránh chúng vì chúng có thể khiến mã khó hiểu và gây bug. Nhưng đôi khi, trong một vài trường hợp, thì nó có thể cũng cấp một lợi ích rõ ràng và dễ đọc.

# Summary - Tổng kết

Giant expressions are hard to think about. This chapter showed a number of ways to break them down so the reader can digest them piece by piece.
Giant expressions rất khó để nghĩ. Chương này đã cho bạn một vài cách để break dơn cho nó người đọc có thể chia chúng thành nhiều mảnh nhỏ.

One simple technique is to introduce “explaining variables” that capture the value of some large subexpression. This approach has three benefits:
Một kĩ thuật đơn giản đó là "explaninig variables" là đặt giá trị của một biểu thức to vào một biểu thức con. Cách tiếp cận này có 3 lợi ích:

• It breaks down a giant expression into pieces.
• Nó break down một giant expresssion thành nhiều mảnh.
• It documents the code by describing the subexpression with a succinct name.
• Nó ghi lại code bằng cách mô tả hiểu thức với tên ngắn ngon
• It helps the reader identify the main “concepts” in the code.
• Nó giúp người đọc xác định được main conccept của code.

Another technique is to manipulate your logic using De Morgan’s laws—this technique can sometimes rewrite a boolean expression in a cleaner way (e.g., `if (!(a && !b))` turns into `if (!a || b))`.
Một kĩ thuật khác là tận dụng logic sử dụng trong quy tắc De Morgan - là một kĩ thuật để viết lại một biểu thức boolean trông ok hơn.

We showed an example where a complex logical condition was broken down into tiny statements like “if (a < b) ...”. In fact, all of the improved-code examples in this chapter had if statements with no more than two values inside them. This setup is ideal. It may not always seem possible to do this—sometimes it requires “negating” the problem or considering the opposite of your goal.
Chúng ta xem các ví dụ có những biểu thức điều kiện phức tạp được chia nhỏ thành các biểu thức bé hơn như `if (a<b)`. Thật ra thì, tất cả cải tiến code trong các vị dụng tỏng trường này đều có `if` với không quá 2 giá trị bên trong. Đó là ý định bạn đâu. Có vẻ như không phải lúc nào cũng có thể thực hiện được điều này, đôi khi nó đòi hỏi phải phủ nhận vấn đề về vấn đề này hoặc xem xét điều ngược lại với mục tiêu của bạn. 

Finally, even though this chapter is about breaking down individual expressions, these same techniques often apply to larger blocks of code, too. So be aggressive in breaking down complex logic wherever you see it.
Cuối cùng, mặc dù chương này breakdown các expression, các kỹ thuật tương tự cũng thường được áp dụng cho các khối lớn hơn. Vì vậy, hãy tích cục phá vỡ những logic phứ tập bất cứ khi nào bạn nìn thấy nó.


























