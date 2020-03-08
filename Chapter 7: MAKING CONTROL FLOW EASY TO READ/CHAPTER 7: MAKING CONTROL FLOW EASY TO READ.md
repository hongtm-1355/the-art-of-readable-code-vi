- Chap 7
===

# CHAPTER 7: MAKING CONTROL FLOW EASY TO READ

![](https://i.imgur.com/t47f91m.png)

Nếu code không có các câu điều kiện (conditionals), lặp hay bất kỳ các câu lệnh điều kiển luồng nào (control flow) thì code sẽ đọc rất dễ dàng. Các câu lệnh điều khiển có thể đôi khi làm ra rất khó để đọc. Chương này nói về cách làm cho controll flow trong code của bạn dễ đọc hơn.

If code had no conditionals, loops, or any other control flow statements, it would be very easy to read. These jumps and branches are the hard stuff, where code can get confusing quickly. This chapter is about making the control flow in your code easy to read.

> Key IDEA
> Biến tất cả các câu điều kiện, lặp hay các câu điều khiển luồng thật tự nhiên nhất có thể, hãy viết sao cho người đọc không phải dừng lại và đọc lại code của bạn.
> 
> Make all your conditionals, loops, and other changes to control flow as “natural” as possible—written in a way that doesn’t make the reader stop and reread your code.


## Thứ tự của các đối số trong câu điều kiện
## The Order of Arguments in Conditionals

Cái nào trong hai đoạn mã sau dễ đọc hơn:
Which of these two pieces of code is more readable:
```
if (length >= 10)
```
hoặc
or
```
if (10 <= length)
```

Hầu hết lập trình viên nói đoạn số một dễ đọc hơn so với đoạn mã số hai, và nó cũng tương tự như đoạn mã tiếp theo:
To most programmers, the first is much more readable. But what about the next two:
```
while (bytes_received < bytes_expected)
```
hoặc
or
```
while (bytes_expected > bytes_received)
```

Phiên bản đầu tiên luôn dễ đọc hơn, nhưng tại sao ? Quy tắc chung là gì ? Làm thế nào để bạn quyết định cái nào tốt hơn `a < b` hay `b > a` ?
Đây là một hướng dẫn chúng tôi thấy nó rất hữu ích:
Again, the first version is more readable. But why? What’s the general rule? How do you decide
whether it’s better to write a < b or b > a?


| Bên trái | Bên phải | 
| -------- | -------- | 
| Biểu thức bị so sánh| Biểu thức được (đem ra) so sánh, có giá trị cố định nhiều hơn | 

| Left-hand side | Right-hand side | 
| -------- | -------- | 
| The expression “being interrogated,” whose value is more in flux.| The expression being compared against, whose value is more constant| 

Hướng dẫn này nó gần gũi với cách dùng tiếng anh, nó rất tự nhiên như `nếu bạn kiếm ít hơn $100/ năm` hoặc `nếu bạn nhỏ hơn 18 tuổi` và đây là cách không được tự nhiên: `nếu 18 tuổi nhỏ hơn hoặc bằng tuổi của bạn`.

This guideline matches English usage—it’s pretty natural to say, “if you make at least $100K/year” or “if you are at least 18 years old.” It’s unnatural to say, “if 18 years is less than or equal to your age.”

Tại sao `while (bytes_received < bytes_expected)` lại dễ đọc hơn, `bytes_received` có giá trị mà chúng ta đang kiểm tra và nó tăng lên theo mỗi lần lặp, `bytes_expected` có giá trị ổn định hơn, và được dùng để so sánh.

This explains why while (bytes_received < bytes_expected) is more readable. bytes_received is the value that we’re checking up on, and it’s increasing as the loop executes. bytes_expected is the more “stable” value being compared against.

> “YODA NOTATION”: STILL USEFUL?
> “YODA NOTATION”: CÒN HỮU DỤNG?
> 

Một vài ngôn ngữ (bao gồm C, C++ nhưng không phải Java), việc đặt biểu thức gán (assignment) bên trong câu lệnh `if` là hợp lệ:
In some languages (including C and C++, but not Java), it’s legal to put an assignment inside an if condition:
```
if (obj = NULL) ...
```
Rất có thể đó là một bug và lập trình viên thực sự muốn nó như này:
Most likely this is a bug, and the programmer actually meant:
```
if (obj == NULL) ...
```
Để ngăn chặn các lỗi như vậy, nhiều lập trình viên thay đổi thứ tự tham số như sau:
To prevent bugs like this, many programmers switch the order of arguments:

```
if (NULL == obj) ...
```

Bằng cách này, `if ==` được viết vô tình là `=`, biểu thức `if (NULL = obj)` không được biên dịch. Thật may, việc đổi thứ tự khiến code không tự nhiên cho việc đọc. (Như Yoda nói, "Không phải là nếu bất kỳ điều gì để nói về nó tôi có"). Rất may, các trình biên dịch hiện đại sẽ cảnh báo code `if (obj = NULL)`, vậy "Yoda Notation" đã trở thành quá khứ.

This way, if == is accidentally written as =, the expression if (NULL = obj) won’t even compile. Unfortunately, switching the order makes the code a bit unnatural to read. (As Yoda would say, “Not if anything to say about it I have.”) Thankfully, modern compilers warn against code like if (obj = NULL), so “Yoda Notation” is becoming a thing of the past


## Thứ tự của if/else block
## The order of if/else blocks

<Anh>/71

Khi viết một câu lệnh if/else, ta thường tự do thay đổi thứ tử của các khối block. Ví dụ bạn có thể viết như sau
When writing an if/else statement, you usually have the freedom to swap the order of the blocks. For instance, you can either write it like.

```js
if(a == b) {
    // case one
} else {
    // case two
}
```

hoặc

```js
if(a != b) {
    // case two
} else {
    // case one
}
```

Bạn có thể không nghĩ nhiều tới việc này trước đây, nhưng trong một số trường hợp, có những lý do chính đang để ưu tiên thứ tự của chúng:
You may not have given much thought about this before, but in some cases there are good reasons to prefer one order over the other:

* Ưu tiên xử lý các trường hợp positive (thể khẳng định) thay vì phủ đụng. Ví dụ: `if(debug)` thay vì `if(!debug)`.
* Prefer dealing with the positive case first instead of the negative—e.g., if (debug) instead of if (!debug).

* Ưu tiên xử lý với những trường hợp đơn giản trước để giải quyết vấn đề này. Các tiếp cận này cũng có thê cho phép cả if và else cùng một lúc.
* Prefer dealing with the simpler case first to get it out of the way. This approach might also allow both the if and the else to be visible on the screen at the same time, which is nice.

* Ưu tiên xử lý trường hợp thú vị hoặc dễ thấy trước
* Prefer dealing with the more interesting or conspicuous case first.

Đôi khi việc này gây ra mâu thuẫn và bạn phải quyết định sự lựa chọn. Nhưng trong nhiều thường hợp, viết rõ ràng sẽ chiến thắng.
Sometimes these preferences conflict, and you have to make a judgment call. But in many cases, there is a clear winner.

Ví dụ, giải sử bạn có một web server sẽ trả về response nếu param `expand_all`:
For example, suppose you have a web server that’s building a response based on whether the URL contains the query parameter expand_all
```js
if (!url.HasQueryParameter("expand_all")) {
    response.Render(items);
    ...
} else {
    for (int i = 0; i < items.size(); i++) {
        items[i].Expand();
    }
    ...
}

```

Khi ngược đọc liếc qua dòng đầu tiên, trong đầu họ ngay lập tức nghĩ về trường có `expand_all`. Giống như việc ai đó nói "Đừng nghĩ về con voi màu hồng". Bạn không thể giúp vẫn nghĩ về nó. "Từ don't" để nhấn mạnh "con voi màu hồng" nhằm gây ấn tượng trong tâm trí ta.
When the reader glances at the first line, her brain immediately thinks about the expand_all case. It’s like when someone says, “Don’t think of a pink elephant.” You can’t help but think about it—the “don’t” is drowned out by the more unusual “pink elephant.

Ở đây, `expand_all` là con voi màu hồng. Bởi vì nó thú vị (hơn trường hợp sử dụng thể khẳng định), trước tiên hãy xem nó trước:

```js
if (url.HasQueryParameter("expand_all")) {
    for (int i = 0; i < items.size(); i++) {
        items[i].Expand();
    }
    ...
} else {
    response.Render(items);
    ...
}

```

Mặt khác, đây là một tình huống mà trường hợp phủ định lại đơn giản và thú vị/nguy hiểm hơn, hãy xem ví dụ:
On the other hand, here’s a situation where the negative case is the simpler and more interesting/dangerous one, so we deal with it first:

```python
if not file:
    # Log the error ...
else:
    # ...
```


Again, depending on the details, this may be a judgment call

Tóm lại, lời khuyên rất đơn giản là chú ý đến các thứ tự khối `if/else`.
To summarize, our advice is simply to pay attention to these factors and watch out for cases where your if/else is in an awkward order.

## The ?: Conditional Expression (a.k.a. “Ternary Operator”)
## ?: Biểu thức điều kiện - toán tử ba ngôi

Trong C, bạn có thể viết một biểu thức điều kiện kiểu `cond ? a : b`, về cơ bản nó là cách viết tắt của `if (cond) { a } else { b }`
In C-like languages, you can write a conditional expression as cond ? a : b, which is essentially a compact way to write if (cond) { a } else { b }.

Ảnh hưởng của nó tới việc đọc đã gây ra tranh cãi. Những người đề xướng nghĩ thằng nó khá tốt để viết những thứ nhiều dòng trên một dòng. Những người phản đối thì cho rằng, nó sẽ gây khó khăn cho việc đọc và khó khăn trong việc debugger theo các step.
Its effect on readability is controversial. Proponents think it’s a nice way to write something in one line that would otherwise require multiple lines. Opponents argue that it can be confusing to read and difficult to step through in a debugger

Dưới đây là ví sử dụng toán tử ba ngôi, mà nó khá dễ đọc và đơn giản.
Here’s a case where the ternary operator is readable and compact:
```
time_str += (hour >= 12) ? "pm" : "am";
```

Nếu không dùng toán tử ba ngôi, bạn có thể viết như sau: 
Avoiding the ternary operator, you might write:
```
if (hour >= 12) {
 time_str += "pm";
} else {
 time_str += "am";
}
```
sẽ có một chút thừa thãi. Trong trường hợp này, một biểu thức điều kiện cũng dễ đọc..
which is a bit drawn out and redundant. In this case, a conditional expression seems reasonable.

Tuy nhiên, những biểu thức này nhanh chóng trở lên khó đọc:
However, these expressions can quickly become difficult to read:
```
return exponent >= 0 ? mantissa * (1 << exponent) : mantissa / (1 << -exponent);
```
Đây, toán tử ba ngôi không còn là sự lựa chọn giữa hai giá trị.Động lực để viết được dòng trên giống như việc "cố gắng ngồi nhét mọi thứ trên cùng một dòng".
Here, the ternary operator is no longer just choosing between two simple values. The motivation for writing code like this is usually to “squeeze everything on one line.”

> Key IDEA

> Instead of minimizing the number of lines, a better metric is to minimize the time needed for someone to understand it.
> Thay vì tập trung giảm thiểu số lượng dòng, hãy tìm cách giảm thiểu thời gian mà người khác hiểu nó.


> ADVICE - Lời khuyên
> Chỉ sử dụng `?: - toán tử ba ngôi` trong trường hợp đơn giản, còn không thì hãy dùng câu lệnh if/else 
> By default, use an if/else. The ternary ?: should be used only for the simplest cases.


## Avoid do/while Loops
## Tránh dùng do/while
Nhiều ngôn ngữ lập trình có một biểu thức do/while. Biểu thức được thực hiện ít nhất một lần. Dưới đây là một ví dụ:
// Tìm kiếm trong danh sách, bắt đầu từ 'nde', với 'name' cho trước.
// Đừng để ý nhiều tới 'max_length'.
Many respected programming languages, as well as Perl, have a do { expression } while
(condition) loop. The expression is executed at least once. Here’s an example:
// Search through the list, starting at 'node', for the given 'name'.
// Don't consider more than 'max_length' nodes.
```java
public boolean ListHasNode(Node node, String name, int max_length) {
 do {
 if (node.name().equals(name))
 return true;
 node = node.next();
 } while (node != null && --max_length > 0);
 return false;
}
```
Điều lạ lùng về do/while là khối lệnh có thể hoặc không được thực thi lại dựa trên điều kiện bên dưới nó. Thông thường, các điều kiện logic nằm ở phần trên code họ bảo vệ điều này là cách nó hoạt động với if, while và cho các câu lệnh. Bởi vì bạn thường đọc code từ từ trên xuống dưới, điều này làm do/white không được tự nhiên. Người đọc có thể sẽ phải đọc từ dưới lên hai lần. Vòng lặp `while` đẽ đọc hơn bởi vì bạn đã biết được điều kiện cho các lần lặp trước khi bạn đọc đoạn code bên trong. Nhưng sẽ thật ngớ ngẩn khi cố gắng thay thế do/while bằng while. (ý là đặt biểu thức điều kiện lên trên)

What’s weird about a do/while loop is that a block of code may or may not be reexecuted based on a condition underneath it. Typically, logical conditions are above the code they guard—this is the way it works with if, while, and for statements. Because you typically read code from top to bottom, this makes do/while a bit unnatural. Many readers end up reading the code twice. While loops are easier to read because you know the condition for all iterations before you read the block of code inside. But it would be silly to duplicate code just to remove a do/while:

```
// Imitating a do/while — DON'T DO THIS! Bắt chước do/while - ĐỪNG LÀM VIỆC ĐÓ!
body
while (condition) {
 body (again)
}

```

May mắn thay, chúng tôi đã phát hiện ra rằng trong thực tế, hầu hết các vòng lặp do/while có thể được viết như là các vòng `white`:
Fortunately, we’ve found that in practice most do/while loops could have been written as while loops to begin with:
```
public boolean ListHasNode(Node node, String name, int max_length) {
 while (node != null && max_length-- > 0) {
 if (node.name().equals(name)) return true;
 node = node.next();
 }
 return false;
}
```

Đoạn code này nó vẫn hoạt động nếu max_length bằng 0 hoặc nếu node là null. Một lý do khác để ta không nên `do/while` là câu lệnh `continue` sử dụng bên trong block có thể gây nhầm lẫn. Ví dụ, đoạn code này đang làm gì?
This version also has the benefit that it still works if max_length is 0 or if node is null. Another reason to avoid do/while is that the continue statement can be confusing inside it. For instance, what does this code do?
```
do {
 continue;
} while (false);
```
Nó sẽ lặp liên tục hay chỉ một lần? Hầu hết lập trình viên sẽ dừng lại và suy nghĩ về nó. (Nó lên chỉ lặp một lần)
Does it loop forever or just once? Most programmers have to stop and think about it. (It should
loop just once.)

Nhìn chung, Bjarne Stroustrup, người tạo ra C ++, nói điều đó là tốt (trong Ngôn ngữ lập trình C ++):
Overall, Bjarne Stroustrup, the creator of C++, says it best (in The C++ Programming Language):
> Theo kinh nghiệm của tôi, do-statement là nguồn gốc gây ra lỗi và nhầm lẫn. Tôi thích đặt "điều kiện" lên phía trên, nơi mà tôi có thể nhìn thấy. 
> In my experience, the do-statement is a source of errors and confusion. … I prefer the condition “up front where I can see it.” Consequently, I tend to avoid do-statements.

## Hãy return sớm nhất có thể trong function
## Returning Early from a Function

Một vài coder tin rằng các hàm không nên có nhiều câu lệnh `return`. Điều này vô lý. Return sớm trong một function hoàn toàn tốt và được mọi người ưa thích. Ví dụ
Some coders believe that functions should never have multiple return statements. This is nonsense. Returning early from a function is perfectly fine—and often desirable. For example:

```
public boolean Contains(String str, String substr) {
 if (str == null || substr == null) return false;
 if (substr.equals("")) return true;
```

Việc implement một function mà không có các guard clauses (mệnh đề bảo vệ) sẽ rất không tự nhiên.
Implementing this function without these “guard clauses” would be very unnatural.

One of the motivations for wanting a single exit point is so that all the cleanup code at the bottom of the function is guaranteed to be called. But modern languages offer more sophisticated ways to achieve this guarantee:

| Language | Structured idiom for cleanup code |
| -------- | -------- |
| C++   | destructors     |
| Java, Python  | try finally     |
| Python   | with     |
| C#   | using     |

Trong C, không có cơ chế để kích thoạt đoạn code khi function exit. Nên nếu có một hàm lớn, mà không có việc cleanup, return sớm có thể rất khó chính xác. Trong trường hợp này, một option có thể được sử dụng là ref hoặc sử dụng ` goto cleanup;.`
In pure C, there is no mechanism to trigger specific code when a function exits. So if there’s a large function with a lot of cleanup code, returning early may be difficult to do correctly. In this case, other options include refactoring the function or even judicious use of goto cleanup;.

## The Infamous goto
Tron ngôn ngữ khác C, thường không có nhu cầu sử dụng `gôto` bởi vì có nhiều cách tốt hơn để làm việc đó. `goto`  cũng được gọi là khá khó kiểm soát.
In languages other than C, there is little need for goto because there are so many better ways to get the job done. gotos are also notorious for getting out of hand quickly and making code difficult to follow.

Nhưng bạn có thể vẫn thấy `goto` được sử dụng ở nhiều project C - hầu hết là trong Linux kernel. Trước khi bạn loại bỏ tất cả việc sử dụng `goto`, sẽ hữu ích hơn khi phân tích lý do tại sao một số người sử dụng `goto` lại tốt hơn những người khác.
But you can still see goto used in various C projects—most notably the Linux kernel. Before you dismiss all use of goto as blasphemy, it’s useful to dissect why some uses of goto are better than others.

Đơn giản nhất, việc sử dụng `goto` vô tội vạ là đặt một điểm exit duy nhất ở dưới cùng của function.
The simplest, most innocent use of goto is with a single exit at the bottom of a function

```
 if (p == NULL) goto exit;
 ...
exit:
 fclose(file1);
 fclose(file2);
 ...
 return;
 
```
Nếu đây là tiêu chuẩn `goto` được phép, thì nó sẽ gặp vấn đề.
If this were the only form of goto allowed, goto wouldn’t be much of a problem.

Vấn đề có thể thới khi có nhiều mục tiêu để `goto`. Cụ thể, `goto` có thể khiến tạo ra các đoạn mã rối rắng và chúng chắn chắn có thể được thay thế bằng lệnh lặp. Vậy, hãy tránh `goto`.

The problems can come when there are multiple goto targets, especially when their paths cross. In particular, gotos that go upward can make for real spaghetti code, and they can surely be replaced with structured loops. Most of the time, goto should be avoided.

## Minimize Nesting
## Giảm Nesting

Lồng code quá nhiều sẽ rất khó hiểu. Mỗi tầng trong một cái nested (lồng nhau) sẽ thêm một điều kiện tới não của người đọc. Khi người đọc nhìn thấy một dấu ngoặc nhọn (}), thật khó để có thể nhớ những điều kiện bên dưới.

Deeply nested code is hard to understand. Each level of nesting pushes an extra condition onto the reader’s “mental stack.” When the reader sees a closing brace (}) it can be hard to “pop” the stack and remember what condition is underneath.

Dưới đây là một ví dụ tương đối đơn giản về điều này.
Here is a relatively simple example of this—see if you notice yourself looking back up to doublecheck which block conditions you’re in:

```
if (user_result == SUCCESS) {
 if (permission_result != SUCCESS) {
 reply.WriteErrors("error reading permissions");
 reply.Done();
 return;
 }
 reply.WriteErrors("");
} else {
 reply.WriteErrors(user_result);
}
reply.Done();
```
Khi bạn thấy ngoặc `}` đầu tiên, bạn sẽ tự nghĩ, Oh, `permission_result != SUCCESS` sẽ kết thúc, bây giờ tới `permission_result == SUCCESS`, nó nằm trong đoạn code của `user_result == SUCCESS`.
When you see that first closing brace, you have to think to yourself, Oh, permission_result != SUCCESS has just ended, so now permission_result == SUCCESS, and this is still inside the block where user_result == SUCCESS

Nhìn chung, bạn có phải giữ giá trị của `user_result` và `permission_result` trong đầu. Và mỗi `if {}`, bạn sẽ chuyển đổi lại giá trị tương ứng trong đầu bạn.
Overall, you have to keep the values of user_result and permission_result in your head at all times. And as each if { } block closes, you have to toggle the corresponding value in your mind.

Đoạn code này kahs tệ vì nó cứ xen kẽ giữa các tình huống `SUCCESS` và không `nonSUCCESS`. Đau đầu.
This particular code is even worse because it keeps alternating between the SUCCESS and nonSUCCESS situations.

## How Nesting Accumulates

Trước khi chúng ta cố gắng fix đoạn code ở ví dụ trước, hay nói về làm thế nào kết thúc theo cách nó sẽ làm.
Before we try to fix the previous example code, let’s talk about how it ended up the way it did.

Ban đầu, đoạn code đơn giản như này:
Originally, the code was simple:

```
if (user_result == SUCCESS) {
 reply.WriteErrors("");
} else {
 reply.WriteErrors(user_result);
}
reply.Done();

```
Đoạn code này khá dễ hiểu, nó đưa trả lại thông báo lỗi, và sau đó, và khi nó xong, nó sẽ `reply`.
This code is perfectly understandable—it figures out what error string to write, and then it’s done with the reply

Nhưng khi lập trình viên thêm một hành vi:
But then the programmer added a second operation:

```
if (user_result == SUCCESS) {
 **if (permission_result != SUCCESS) {
 reply.WriteErrors("error reading permissions");
 reply.Done();
 return;
 }**
 reply.WriteErrors("");
```

Sự thay đổi này có nghĩa là người lập trình có một đoạn mã mới để chèn và cô ấy đã tìm thấy nơi dễ nhất để chèn nó. Code mới này đã được in đậm trong đầu của cô ấy. Và những khác biệt về sự thay đổi này là rất rõ ràng, nó trông giống như một sự thay đổi đơn giản. (ý là đầu của cô ấy)

This change makes sense—the programmer had a new chunk of code to insert, and she found the easiest place to insert it. This new code was fresh and mentally “bolded” in her mind. And the “diff” of this change is very clean—it looks like a simple change.

Nhưng khi một ai đó đọc lại code sau đó, tất cả bối cánh biến mất. Nó như cách mà bạn lần đầu tiên đọc mã ở đầu phần này.
But when someone else comes across the code later, all that context is gone. This is the way it was for you when you first read the code at the beginning of this section—you had to take it in all at once.

> KEY IDEA
> Look at your code from a fresh perspective when you’re making changes. Step back and look at it as a whole.
> Nhìn vào code của bạn từ góc nhìn khác khi bạn thực hiện các thay đổi. Lùi lại và nhìn nó một cách tổng thể hơn.

## Removing Nesting by Returning Early
## Dùng return thay thế việc lồng lệnh

Okay, hãy cải thiện lại đoạn code kia. Nesting như thế kia có thể được loại bỏ bằng cách xử lý "trường hợp fail" càng sớm càng tốt và return sớm nhất có thể:
Okay, so let’s improve the code. Nesting like this can be removed by handling the “failure cases” as soon as possible and returning early from the function:
```
if (user_result != SUCCESS) {
 reply.WriteErrors(user_result);
 reply.Done();
 return;
}
if (permission_result != SUCCESS) {
 reply.WriteErrors(permission_result);
 reply.Done();
 return;
}
reply.WriteErrors("");
reply.Done();
```
Đoạn code này chỉ có một tầng nesting, thay vì 2 như cũ. Nhưng quan trọng hơn, người đọc không bao giờ "pop" bất cứ thứ gì vào ngăn xếp của họ nếu kết thúc block là một câu `return`. (ý là không phải nhớ nhiều)
This code only has one level of nesting, instead of two. But more importantly, the reader never has to “pop” anything from his mental stack—every if block ends in a return.

## Removing Nesting Inside Loops
## Loại bỏ việc lồng lệnh trong vòng lặp

Kỹ thuật return sớm không phải lúc nào cũng được áp dụng. Ví dụ, ở đây, trường hợp code được lồng bên trong một vòng lặp:
The technique of returning early isn’t always applicable. For example, here’s a case of code nested in a loop:

```
for (int i = 0; i < results.size(); i++) {
 if (results[i] != NULL) {
     non_null_count++;
     if (results[i]->name != "") {
     cout << "Considering candidate..." << endl;
     ...
     }
 }
}
```
Bên trong vòng lặp, kỹ thuật tương tự như `return` là dùng `continue`:
Inside a loop, the analogous technique to returning early is to continue:

```
for (int i = 0; i < results.size(); i++) {
 if (results[i] == NULL) continue;
     non_null_count++;
 if (results[i]->name == "") continue;
 cout << "Considering candidate..." << endl;
 .
}
```
Trong cùng một cách, một `if (...) return;` hoặc động như một mệnh đề bảo vệ cho hàm, `if (...) continue;` thực sự giống một mệnh đề bảo vệ cho vòng lặp.

Nhìn chung, `continue` có thể gây nhầm lẫn, bởi vì nó sẽ thoát ra khỏi lần lặp, giống như `goto` bên trong loop. Nhưng trong trường hợp này, mỗi lần lặp của loop là độc lập, người đọc có thể dễ dàng thấy rằng `continue` có nghĩa là `bỏ qua item này`.
In general, the continue statement can be confusing, because it bounces the reader around, like a goto inside the loop. But in this case, each iteration of the loop is independent (the loop is a “for each”), so the reader can easily see that continue just means “skip over this item.”

## Can You Follow the Flow of Execution?
## Bạn có thể theo dõi được flow của code ?

![](https://i.imgur.com/8H7zX6F.png)

Chương này nói về low-level trong điều khiển luồng như: cách tạo vòng lặp, điều kiện và các điều kiện nhảy khác cho dễ đọc. Nhưng bạn cũng nên suy nghĩ về flow của chương trình ở cấp độ cao hơn. Lý tưởng nhất là bạn có thể dễ dàng theo dõi toàn bộ đường đi thực thi chương trình của mình bắt đầu với `main ()` và tiếp theo là tới các hàm được gọi cho tới khi chương trình kết thúc.
This chapter has been about low-level control flow: how to make loops, conditionals, and other jumps easy to read. But you should also think about the “flow” of your program at a high level.Ideally, it would be easy to follow the entire execution path of your program—you’d start atmain() and mentally step through the code, as one function calls another, until the programexits.

Tuy nhiên, trên thực tế, các ngôn ngữ lập trình và thư viện có các cấu trúc cho code được thực hiện ở đằng sau, rất khó follow. Dưới đây là một số ví dụ:
In practice, however, programming languages and libraries have constructs that let code execute “behind the scenes” or make it difficult to follow. Here are some examples:

| Programming construct | How high-level program flow gets obscured |
| -------- | -------- | 
| threading | It’s unclear what code is executed when    |
| signal/interrupt handlers  | Certain code might be executed at any time.    |
| exceptions | Execution can bubble up through multiple function calls.    |
| function pointers & anonymous functions | It’s hard to know exactly what code is going to run because that isn’t known at compile time.    |
| virtual methods | `object.virtualMethod()` might invoke code of an unknown subclass.    |

Một số cấu trúc này rất hữu ích và thậm chí chúng có thể làm cho code của bạn dễ đọc hơn và ít dư thừa. Nhưng là lập trình viên, đôi khi chúng ta bị mang đi và sử dụng chúng quá mức mà không nhận ra người đọc sẽ khó hiểu mã sau này như thế nào. Ngoài ra, các cấu trúc này làm cho debug trở lên khó khăn hơn rất nhiều.
Some of these constructs are very useful, and they can even make your code more readableand less redundant. But as programmers, sometimes we get carried away and use themexcessively without realizing how difficult it will be for readers to understand the code later.Also, these constructs make bugs much harder to track down.

The key is to not let too large a percentage of your code use these constructs. If you abuse thesefeatures, it can make tracing through your code like a game of Three-Card Monte (as in thecartoon)

## Summary
## Tổng kết

Có một số điều bạn có thể khiến flow code của bạn dễ đọc hơn.
There are a number of things you can do to make your code’s control flow easier to read.

Khi viết một biểu thức so sánh `(while (bytes_expected > bytes_received))`, tốt nhất là đặt giá trị thay đổi bên trái và giá trị ổn định hơn bên phải `(while (bytes_received <bytes_expected))`
When writing a comparison `(while (bytes_expected > bytes_received))`, it’s better to put the changing value on the left and the more stable value on the right `(while (bytes_received < bytes_expected))`.

Bạn cũng có thể sắp xếp lại các khối lệnh `if/else`. Nói chung, trước tiên hãy cố gắng xử lý trường hợp `khẳng định/dễ dàng/thú vị` trước. Đôi khi những tiêu chí này mâu thuẫn, nhưng nếu không, thì đó là một quy tắc tốt để tuân theo.
You can also reorder the blocks of an if/else statement. Generally, try to handle the positive/ easier/interesting case first. Sometimes these criteria conflict, but when they don’t, it’s a good rule of thumb to follow.

Trong một số cấu trúc lập trình nhất định, như toán tử ternary `(:?)`, Vòng lặp `do/while` và `goto` thường dẫn đến code không thể đọc được. Tốt nhất không sử dụng chúng, vì luôn có các lựa chọn thay thế rõ ràng và tốt hơn.
Certain programming constructs, like the ternary operator (: ?), the do/while loop, and goto often result in unreadable code. It’s usually best not to use them, as clearer alternatives almost always exist.




