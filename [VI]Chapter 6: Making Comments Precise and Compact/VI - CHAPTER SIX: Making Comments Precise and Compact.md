# CHAPTER SIX: Making Comments Precise and Compact
# Chương 6: Comment chính xác và gọn nhẹ
![](https://i.imgur.com/dToTytG.png)


The previous chapter was about realizing what you should be commenting. This chapter is about how to write comments that are precise and compact.
Ở chương trước, chúng ta đã nói về những gì bạn nên bình luận. Ở chương này nói về cách viết bính luận chính xác và cô đọng.

If you're going to write a comment at all, it might as well be precise—as specific and detailed as possible. On the other hand, comments take up extra space on the screen, and take extra time to read. So comments should also be compact.
Nếu bạn định viết một bình luận, nó có thể chính xác - cụ thể và chi tiết nhất có thể. MẶt khác, các commens chiêms một khoảng không gian màn hình và tốn thời gian hơn để đọc. Vì thế bình luận này nên cô đọng.

> K E Y I D E A
> Comments should have a high information-to-space ratio.
> Comments phải có tỷ lệ thông tin trên không gian cao.

The rest of the chapter shows examples of how to do this.

## Keep Comments Compact

## Giữ nhận xét gọn gàng


Đây là một ví dụ về comment trong C++
Here’s an example of a comment for a C++ type definition:
```
// The int is the CategoryType. int là CategoryTYpe
// The first float in the inner pair is the 'score', Giá trị đầu tiên trong par là điểm
// the second is the 'weight'. giá trị số hai là trọng sô
typedef hash_map<int, pair<float, float> > ScoreMap;
```
But why use three lines to explain it, when you can illustrate it in just one line?
Nhưng tại sao phải dùng ba dòng để giải thích nó, khi bạn có thể minh họa nó chỉ trong một dòng?
```
// CategoryType -> (score, weight)
typedef hash_map<int, pair<float, float> > ScoreMap;
```

Some comments need three lines of space, but this is not one of them.
Một số nhận xét có thể cần ba dòng, nhưng đây không phải là một trong số đó.

## Avoid Ambiguous Pronouns
## Tránh đại từ mô hồ

As the classic “Who’s on First?” skit illustrated, pronouns can make things very confusing.
Như câu như “Ai là người đầu tiên?” tiểu phẩm minh họa, đại từ có thể khiến mọi thứ trở nên rất khó hiểu.

It takes extra work for the reader to “resolve” a pronoun. And in some cases, it’s unclear what “it” or “this” is referring to. Here’s an example:
Người đọc phải mất thêm công sức để “giải quyết” một đại từ. Và trong một số trường hợp, không rõ “nó” hoặc “điều này” đang đề cập đến điều gì. Đây là một ví dụ:
```
// Insert the data into the cache, but check if it's too big first.
```
In this comment, “it” might refer to the data or the cache. You could probably figure that out by reading the rest of the code. But if you have to do that, what’s the point of the comment?
Trong nhận xét này, "nó" có thể đề cập đến dữ liệu hoặc bộ nhớ cache. Bạn có thể tìm ra điều đó bằng cách đọc phần còn lại của mã. Nhưng nếu bạn phải làm như vậy, ý kiến ​​của nhận xét là gì?

The safest thing is to “fill in” pronouns if there’s any chance of confusion. In the previous example, let’s assume “it” was “the data”:

Điều an toàn nhất là "điền vào" đại từ nếu có bất kỳ trường hợp nhầm lẫn nào. Trong ví dụ trước, giả sử “nó” là “dữ liệu”:
```
// Insert the data into the cache, but check if the data is too big first.
```
This is the simplest change to make. You could also have restructured the sentence to make “it” perfectly clear:
Đây là thay đổi đơn giản nhất để thực hiện. Bạn cũng có thể đã cấu trúc lại câu để làm cho “nó” hoàn toàn rõ ràng:
```
// If the data is small enough, insert it into the cache.
```

## Polish Sloppy Sentences

In many cases, making a comment more precise goes hand-in-hand with making it more compact.
Trong nhiều trường hợp, việc đưa ra nhận xét chính xác hơn đi đôi với việc làm cho nó trở nên gọn gàng hơn.

Here is an example from a web crawler:
Đây là một ví dụ từ trình thu thập thông tin web:
```
# Depending on whether we've already crawled this URL before, give it a different priority.
```
This sentence might seem okay, but compare it to this version:
Câu này có vẻ ổn, nhưng hãy so sánh nó với phiên bản này:
```
# Give higher priority to URLs we've never crawled before.
```
This sentence is simpler, smaller, and more direct. It also explains that higher priority is given to uncrawled URLs—the previous comment didn’t contain that information
Câu này đơn giản hơn, nhỏ hơn và trực tiếp hơn. Nó cũng giải thích rằng mức độ ưu tiên cao hơn được dành cho các URL chưa được thu thập thông tin — nhận xét trước đó không chứa thông tin đó

## Describe Function Behavior Precisely
## Mô tả Hành vi Chức năng Chính xác

Imagine you just wrote a function that counts the number of lines in a file:
Hãy tưởng tượng bạn vừa viết một hàm đếm số dòng trong một tệp:
```
// Return the number of lines in this file.
int CountLines(string filename) { ... }
```
This comment isn’t very precise—there are a lot of ways to define a “line.” Here are some corner cases to think about:
Nhận xét này không chính xác lắm — có rất nhiều cách để xác định số dòng. Dưới đây là một số trường hợp góc cần suy nghĩ:
• "" (an empty file)—0 or 1 line?
• "hello"—0 or 1 line?
• "hello\n"—1 or 2 lines?
• "hello\n world"—1 or 2 lines?
• "hello\n\r cruel\n world\r"—2, 3, or 4 lines?
The simplest implementation is to count the number of newline (\n) characters. (This is the way the Unix command wc works.) Here’s a better comment to match this implementation:
Cách triển khai đơn giản nhất là đếm số ký tự dòng mới (\n). (Đây là cách hoạt động của lệnh Unix `wc`.) Dưới đây là một nhận xét tốt hơn để phù hợp với việc triển khai này:
```
// Count how many newline bytes ('\n') are in the file.
int CountLines(string filename) { ... }
```
This comment isn’t much longer than the first version, but contains much more information.
Nhận xét này không dài hơn nhiều so với phiên bản đầu tiên, nhưng chứa nhiều thông tin hơn.

It tells the reader that the function might return 0 if there are no newlines. It also tells the reader that carriage returns (\r) are ignored.
Nó cho người đọc biết rằng hàm có thể trả về 0 nếu không có dòng mới. Nó cũng cho người đọc biết rằng các dấu xuống dòng (\ r) bị bỏ qua.
## Use Input/Output Examples That Illustrate Corner Cases
## Sử dụng Input.Output Minh họa các Trường hợp
When it comes to comments, a carefully chosen input/output example can be worth a thousand words.
Khi nói đến nhận xét, một ví dụ đầu vào / đầu ra được lựa chọn cẩn thận có thể có giá trị hàng nghìn từ.

For example, here’s a common function that removes parts of a string:
Ví dụ: đây là một hàm phổ biến loại bỏ các phần của một chuỗi:
```
// Remove the suffix/prefix of 'chars' from the input 'src'.
String Strip(String src, String chars) { ... }
```
This comment isn’t very precise because it can’t answer questions such as:
Nhận xét này không chính xác lắm vì nó không thể trả lời các câu hỏi như:
- Is chars a whole substring that is to be removed, or effectively just an unordered set of letters?
- Các ký tự có phải là toàn bộ chuỗi con cần được loại bỏ hay chỉ là một tập hợp không có thứ tự của bức thư?
- What if there are multiples of chars on the end of src?
- Điều gì sẽ xảy ra nếu có nhiều ký tự ở cuối src?

Instead, a well-chosen example can answer these questions:
Thay vào đó, một ví dụ được chọn tốt có thể trả lời những câu hỏi sau:
```
// ...
// Example: Strip("abba/a/ba", "ab") returns "/a/"
String Strip(String src, String chars) { ... }
```
The example “shows off” the full functionality of Strip(). Note that a simpler example wouldn’t be as useful, if it doesn’t answer those questions:
Ví dụ “khoe” toàn bộ chức năng của Strip (). Lưu ý rằng một ví dụ đơn giản hơn sẽ không hữu ích nếu nó không trả lời được những câu hỏi sau:
```
// Example: Strip("ab", "a") returns "b"
```
Here’s another example of a function that could use an illustration:
Dưới đây là một ví dụ khác về một hàm có thể sử dụng hình minh họa:
```
// Rearrange 'v' so that elements < pivot come before those >= pivot;
// Then return the largest 'i' for which v[i] < pivot (or -1 if none are < pivot)
int Partition(vector<int>* v, int pivot);
```
This comment is actually very precise, but a little bit hard to visualize. Here’s an example you could include to illustrate things further:
Nhận xét này thực sự rất chính xác, nhưng hơi khó hình dung. Dưới đây là một ví dụ bạn có thể đưa vào để minh họa thêm những điều:
```
// ...
// Example: Partition([8 5 9 8 2], 8) might result in [5 2 | 8 9 8] and return 1
int Partition(vector<int>* v, int pivot);
```

There are a number of points to mention about the specific example input/output we chose:
Có một số điểm cần đề cập về đầu vào / đầu ra ví dụ cụ thể mà chúng tôi đã chọn:
- The pivot is equal to elements in the vector to illustrate that edge case.
- Trụ bằng các phần tử trong vectơ để minh họa trường hợp cạnh đó.
- We put duplicates in the vector (8) to illustrate that this is an acceptable input.
- Chúng tôi đặt các bản sao trong vectơ (8) để minh họa rằng đây là đầu vào có thể chấp nhận được.
- The resulting vector is not sorted—if it were, the reader might get the wrong idea.
- Vectơ kết quả không được sắp xếp - nếu có, người đọc có thể hiểu sai ý.
- Because the return value was 1, we made sure 1 wasn’t also a value in the vector—that would be confusing.
- Bởi vì giá trị trả về là 1, chúng tôi đảm bảo 1 không phải cũng là một giá trị trong vectơ — điều này sẽ gây nhầm lẫn.

## State the Intent of Your Code
## Nêu ý định của mã của bạn

As we mentioned in the previous chapter, commenting is often about telling the reader what you were thinking about when you wrote the code. Unfortunately, many comments end up just describing what the code does in literal terms, without adding much new information.
Như chúng tôi đã đề cập trong chương trước, bình luận thường là cho người đọc biết bạn đang nghĩ gì khi viết mã. Thật không may, nhiều nhận xét cuối cùng chỉ mô tả những gì mã làm theo nghĩa đen, mà không thêm nhiều thông tin mới.

Here’s an example of such a comment:
Dưới đây là một ví dụ về một nhận xét như vậy:
```
void DisplayProducts(list<Product> products) {
    products.sort(CompareProductByPrice);
    // Iterate through the list in reverse order
        for (list<Product>::reverse_iterator it = products.rbegin(); it != products.rend();
             ++it)
            DisplayPrice(it->price);
    ...
}
```
All this comment does is just describe the line below it. Instead, consider this better comment:
Tất cả những gì nhận xét này chỉ là mô tả dòng bên dưới nó. Thay vào đó, hãy xem xét nhận xét này tốt hơn:
```
// Display each price, from highest to lowest
for (list<Product>::reverse_iterator it = products.rbegin(); ... )
```

This comment explains what the program is doing at a higher level. This is much more in tune with what the programmer was thinking when she wrote the code.
Nhận xét này giải thích những gì chương trình đang làm ở cấp độ cao hơn. Điều này phù hợp hơn nhiều với những gì lập trình viên đã nghĩ khi cô ấy viết mã.

Interestingly, there is a bug in this program! The CompareProductByPrice function (not shown) already sorts higher-priced items first. The code is doing the opposite of what the author intended.
Thật thú vị, có một lỗi trong chương trình này! Chức năng CompareProductByPrice (không hiển thị) đã sắp xếp các mặt hàng có giá cao hơn trước. Đoạn mã đang làm ngược lại với những gì tác giả dự định.

This is a good reason why the second comment is better. Despite the bug, the first comment is technically correct (the loop does iterate in reverse order). But with the second comment, a reader is more likely to notice that the intent of the writer (to show higher-priced items first) contradicts what the code actually does. In effect, the comment acts as a redundancy check.
Đây là một lý do tốt tại sao nhận xét thứ hai tốt hơn. Bất chấp lỗi, nhận xét đầu tiên là đúng về mặt kỹ thuật (vòng lặp lặp lại theo thứ tự ngược lại). Nhưng với nhận xét thứ hai, người đọc có nhiều khả năng nhận thấy rằng mục đích của người viết (để hiển thị các mặt hàng có giá cao hơn trước) mâu thuẫn với những gì mã thực sự làm. Trên thực tế, nhận xét hoạt động như một kiểm tra dư thừa.

Ultimately, the best redundancy check is a unit test (see Chapter 14, Testing and Readability). But it’s still worthwhile having comments like these explaining the intent of your program.
Cuối cùng, kiểm tra dự phòng tốt nhất là kiểm tra đơn vị (xem Chương 14, Kiểm tra và Khả năng đọc). Nhưng vẫn đáng giá khi có những nhận xét như thế này giải thích mục đích của chương trình của bạn.
## “Named Function Parameter” Comments
Nhận xét “Tham số chức năng được đặt tên”
Suppose you saw a function call like this one:
Giả sử bạn đã thấy một lệnh gọi hàm như thế này:
```
Connect(10, false);
```
This function call is a bit mysterious because of those integer and boolean literals being passed in.
Lời gọi hàm này hơi bí ẩn vì các ký tự nguyên và boolean đó được chuyển vào.

In languages like Python, you can assign the arguments by name:
Trong các ngôn ngữ như Python, bạn có thể gán các đối số theo tên:
```
def Connect(timeout, use_encryption): ...
# Call the function using named parameters
Connect(timeout = 10, use_encryption = False)
```
In languages like C++ and Java, you can’t do this. However, you can use an inline comment to the same effect:
Trong các ngôn ngữ như C ++ và Java, bạn không thể làm điều này. Tuy nhiên, bạn có thể sử dụng nhận xét nội tuyến để có hiệu quả tương tự:
```
void Connect(int timeout, bool use_encryption) { ... }
// Call the function with commented parameters
Connect(/* timeout_ms = */ 10, /* use_encryption = */ false);
```
Notice that we “named” the first parameter timeout_ms instead of timeout. Ideally, the function’s real argument would have been timeout_ms, but if for some reason we can’t make this change, this is a handy way to “improve” the name.
Lưu ý rằng chúng tôi đã “đặt tên” cho tham số đầu tiên là timeout_ms thay vì timeout. Lý tưởng nhất, đối số thực của hàm sẽ là timeout_ms, nhưng nếu vì lý do nào đó mà chúng tôi không thể thực hiện thay đổi này, thì đây là một cách hữu ích để "cải thiện" tên.

When it comes to boolean arguments, it’s especially important to put /* name = */ in front of the value. Putting the comment behind the value is very confusing:
Khi nói đến đối số boolean, điều đặc biệt quan trọng là đặt / * name = * / trước giá trị. Đặt nhận xét đằng sau giá trị là rất khó hiểu:
```
// Don't do this!
Connect( ... , false /* use_encryption */);
// Don't do this either!
Connect( ... , false /* = use_encryption */);
```
In these examples, it’s unclear whether false means “use encryption” or “don’t use encryption”.
Trong các ví dụ này, không rõ liệu false nghĩa là “sử dụng mã hóa” hay “không sử dụng mã hóa”.

Most function calls don’t need comments like these, but it’s a handy (and compact) way to explain mysterious-looking arguments.
Hầu hết các lệnh gọi hàm không cần nhận xét như thế này, nhưng đó là một cách tiện dụng (và nhỏ gọn) để giải thích các đối số có vẻ bí ẩn.
## Use Information-Dense Words
## Sử dụng các từ dày đặc thông tin

Once you’ve been programming for a number of years, you notice that the same general problems and solutions come up repeatedly. Often, there are specific words or phrases that have been developed to describe these patterns/idioms. Using these words can make your comments much more compact.
Khi bạn đã lập trình trong một số năm, bạn nhận thấy rằng các vấn đề và giải pháp chung giống nhau xuất hiện lặp đi lặp lại. Thông thường, có những từ hoặc cụm từ cụ thể đã được phát triển để mô tả những mẫu / thành ngữ này. Sử dụng những từ này có thể làm cho nhận xét của bạn trở nên gọn gàng hơn nhiều.
For example, suppose your comment were:
Ví dụ: giả sử nhận xét của bạn là:
```
// This class contains a number of members that store the same information as in the
// database, but are stored here for speed. When this class is read from later, those
// members are checked first to see if they exist, and if so are returned; otherwise the
// database is read from and that data stored in those fields for next time.
```
Instead, you could just say:
Thay vào đó, bạn chỉ có thể nói:
```
// This class acts as a caching layer to the database.
```
As another example, a comment such as:
Một ví dụ khác, một nhận xét như:
```
// Remove excess whitespace from the street address, and do lots of other cleanup
// like turn "Avenue" into "Ave." This way, if there are two different street addresses
// that are typed in slightly differently, they will have the same cleaned-up version and
// we can detect that these are equal.
```
could instead be:
thay vào đó có thể là:
```
// Canonicalize the street address (remove extra spaces, "Avenue" -> "Ave.", etc.)
```
There are lots of words and phrases that pack a lot of meaning, such as “heuristic,” “bruteforce,” “naive solution,” and the like. If you have a comment that feels a bit long-winded, see if it can be described as a typical programming situation

Có rất nhiều từ và cụm từ mang nhiều ý nghĩa, chẳng hạn như “heuristic”, “bruteforce”, “ngây thơ giải pháp” và những thứ tương tự. Nếu bạn có một nhận xét hơi dài dòng, hãy xem nó có thể được mô tả như một tình huống lập trình điển hình không
## Summary
## Tóm lược
This chapter is about writing comments that pack as much information into as small a space as possible. Here are the specific tips:
- Avoid pronouns like “it” and “this” when they can refer to multiple things.
- Describe a function’s behavior with as much precision as is practical.
- Illustrate your comments with carefully chosen input/output examples.
- State the high-level intent of your code, rather than the obvious details.
- Use inline comments (e.g., Function(/* arg = */ ... ) ) to explain mysterious function arguments.
- Keep your comments brief by using words that pack a lot of meaning

Chương này nói về việc viết các bình luận đóng gói càng nhiều thông tin vào một khoảng trống càng nhỏ càng tốt. Dưới đây là các mẹo cụ thể:
- Tránh các đại từ như “it” và “this” khi chúng có thể ám chỉ nhiều thứ.
- Mô tả hành vi của một hàm với độ chính xác càng cao càng tốt.
- Minh họa nhận xét của bạn bằng các ví dụ đầu vào / đầu ra được lựa chọn cẩn thận.
- Nêu mục đích cấp cao của mã của bạn, thay vì các chi tiết rõ ràng.
- Sử dụng chú thích nội dòng (ví dụ: Hàm (/ * arg = * / ...)) để giải thích các đối số hàm bí ẩn.
- Giữ cho nhận xét của bạn ngắn gọn bằng cách sử dụng các từ có nhiều ý nghĩa




















