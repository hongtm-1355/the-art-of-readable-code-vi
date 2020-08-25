# CHAPTER THIRTEEN: Writing Less Code

![](https://i.imgur.com/J9QClkQ.png)

Knowing when not to code is possibly the most important skill a programmer can learn. Every line of code you write is a line that has to be tested and maintained. By reusing libraries or eliminating features, you can save time and keep your codebase lean and mean.
Biết khi nào không nên viết mã có thể là kỹ năng quan trọng nhất mà một lập trình viên có thể học. Mỗi dòng mã bạn viết là một dòng phải được kiểm tra và duy trì. Bằng cách sử dụng lại các thư viện hoặc loại bỏ các tính năng, bạn có thể tiết kiệm thời gian và giữ cho cơ sở mã của mình gọn gàng và có ý nghĩa.
> KEY IDEA
> The most readable code is no code at all.
>
## Don’t Bother Implementing That Feature—You Won’t Need It
When you start a project, it’s natural to get excited and think of all the cool features you’ll want to implement. But programmers tend to overestimate how many features are truly essential to their project. A lot of features go unfinished or unused or just complicate the application.
Khi bạn bắt đầu một dự án, điều tự nhiên là bạn sẽ hào hứng và nghĩ đến tất cả các tính năng thú vị mà bạn sẽ muốn triển khai. Nhưng các lập trình viên có xu hướng đánh giá quá cao có bao nhiêu tính năng thực sự cần thiết cho dự án của họ. Rất nhiều tính năng chưa hoàn thành hoặc không được sử dụng hoặc chỉ làm phức tạp ứng dụng.

Programmers also tend to underestimate how much effort it takes to implement a feature. We optimistically estimate how long it will take to implement a crude prototype but forget how much extra time is involved in future maintenance, documentation, and the added “weight”
to the codebase.
Các lập trình viên cũng có xu hướng đánh giá thấp cần bao nhiêu nỗ lực để triển khai một tính năng. Chúng tôi ước tính một cách lạc quan sẽ mất bao lâu để thực hiện một nguyên mẫu thô nhưng quên mất bao nhiêu thời gian bổ sung liên quan đến bảo trì, tài liệu trong tương lai và “trọng lượng” bổ sung
đến cơ sở mã.
## Question and Break Down Your Requirements
Not all programs need to be fast, 100% correct, and able to handle every possible input. If you really scrutinize your requirements, sometimes you can carve out a simpler problem that requires less code. Let’s look at some examples of this.

Không phải tất cả các chương trình đều cần phải nhanh, chính xác 100% và có thể xử lý mọi đầu vào có thể. Nếu bạn thực sự xem xét kỹ lưỡng các yêu cầu của mình, đôi khi bạn có thể tìm ra một vấn đề đơn giản hơn, yêu cầu ít mã hơn. Hãy xem một số ví dụ về điều này.
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

Giả sử bạn đang viết “công cụ định vị cửa hàng” cho một doanh nghiệp. Bạn nghĩ rằng yêu cầu của bạn là:
    Đối với bất kỳ vĩ độ / kinh độ nhất định của người dùng, hãy tìm cửa hàng có vĩ độ / kinh độ gần nhất.

Để thực hiện điều này một cách chính xác 100%, bạn cần phải xử lý:
    • Khi các vị trí nằm ở hai bên của Đường Ngày Quốc tế
    • Khi các địa điểm gần Bắc cực hoặc Nam cực
    • Điều chỉnh độ cong của Trái đất, khi "độ dọc mỗi dặm" ​​thay đổi

Xử lý tất cả các trường hợp này cần một lượng mã hợp lý.

Tuy nhiên, đối với ứng dụng của bạn, chỉ có 30 cửa hàng ở bang Texas. Trong khu vực nhỏ hơn này, ba vấn đề trong danh sách không quá quan trọng. Do đó, bạn có thể giảm yêu cầu của mình xuống:
    Đối với người dùng ở gần Texas, hãy tìm (gần đúng) cửa hàng gần nhất ở Texas.

Giải quyết vấn đề này dễ dàng hơn, vì bạn có thể giải quyết vấn đề chỉ với việc lặp lại từng cửa hàng và tính toán khoảng cách Euclid giữa các vĩ độ / vĩ độ.
### Example: Adding a Cache

We once had a Java application that frequently read objects from disk. The speed of the application was limited by these reads, so we wanted to implement caching of some sort. A typical sequence of reads looked like this:
Chúng ta đã từng có một ứng dụng Java thường xuyên đọc các đối tượng từ đĩa. Tốc độ của ứng dụng bị giới hạn bởi những lần đọc này, vì vậy chúng tôi muốn triển khai một số loại bộ nhớ đệm. Một chuỗi lần đọc điển hình trông như thế này:
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
Như bạn có thể thấy, đã có rất nhiều lần truy cập lặp lại vào cùng một đối tượng, vì vậy bộ nhớ đệm chắc chắn sẽ hữu ích.

When faced with this problem, our first instinct was to use a cache that discards the least recently used items. We didn’t have one available in our library, so we would have had to implement it ourselves. That wasn’t a problem though, as we’ve implemented such a data structure before (it involves both a hash table and a singly linked list—perhaps 100 lines of code in total).
Khi đối mặt với vấn đề này, bản năng đầu tiên của chúng tôi là sử dụng bộ nhớ cache để loại bỏ các mục ít được sử dụng gần đây nhất. Chúng tôi không có sẵn một cái trong thư viện của mình, vì vậy chúng tôi sẽ phải tự triển khai nó. Tuy nhiên, đó không phải là vấn đề vì chúng tôi đã triển khai cấu trúc dữ liệu như vậy trước đây (nó liên quan đến cả bảng băm và danh sách được liên kết đơn lẻ — có lẽ tổng cộng 100 dòng mã).

However, we noticed that the repeated accesses were always in a row. So instead of implementing an LRU cache, we just implemented a one-item cache:
Tuy nhiên, chúng tôi nhận thấy rằng các lần truy cập lặp lại luôn liên tiếp. Vì vậy, thay vì triển khai bộ đệm LRU, chúng tôi chỉ triển khai bộ đệm một mục:
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
Điều này mang lại cho chúng tôi 90% lợi ích mà không cần phải viết mã nhiều và chương trình cũng có bộ nhớ nhỏ.
The benefits of “removing requirements” and “solving simpler problems” can’t be overstated.
Requirements often interfere with each other in subtle ways. This means that solving half the problem might only take a quarter as much coding effort.
Không thể phóng đại lợi ích của việc “loại bỏ các yêu cầu” và “giải quyết các vấn đề đơn giản hơn”.
Các yêu cầu thường gây trở ngại cho nhau theo những cách tế nhị. Điều này có nghĩa là việc giải quyết một nửa vấn đề có thể chỉ tốn một phần tư nỗ lực mã hóa.
## Keeping Your Codebase Small

![](https://i.imgur.com/9IWbxOa.png)

When you first start a software project, and you have only one or two source files, things are great. Compiling and running the code is a snap, it’s easy to make changes, and it’s easy to remember where each function or class is defined.
Khi bạn lần đầu tiên bắt đầu một dự án phần mềm và bạn chỉ có một hoặc hai tệp nguồn, mọi thứ thật tuyệt vời. Việc biên dịch và chạy mã diễn ra nhanh chóng, dễ dàng thực hiện các thay đổi và dễ nhớ vị trí của từng hàm hoặc lớp được xác định.

Then, as the project grows, your directory fills up with more and more source files. Soon you need multiple directories to organize them all. It’s harder to remember which functions call which other functions, and tracking down bugs takes a little more work.
Sau đó, khi dự án phát triển, thư mục của bạn ngày càng lấp đầy với nhiều tệp nguồn hơn. Bạn sẽ sớm cần nhiều thư mục để sắp xếp tất cả. Khó nhớ hàm nào gọi hàm nào khác và việc theo dõi lỗi sẽ mất nhiều công hơn một chút.

Eventually, you have lots of source code spread across many different directories. The project is huge, and no single person understands it all. Adding new features becomes painful, and working with the code is cumbersome and unpleasant.
Cuối cùng, bạn có rất nhiều mã nguồn trải rộng trên nhiều thư mục khác nhau. Dự án rất lớn, và không một người nào hiểu hết được. Việc thêm các tính năng mới trở nên khó khăn và làm việc với mã rất cồng kềnh và khó chịu.

What we’ve described is a natural law of the universe—as any coordinated system grows, the complexity needed to keep it glued together grows even faster.
Những gì chúng tôi đã mô tả là quy luật tự nhiên của vũ trụ — khi bất kỳ hệ thống phối hợp nào phát triển, độ phức tạp cần thiết để giữ cho chúng gắn kết với nhau thậm chí còn tăng nhanh hơn.

The best way to cope is to keep your codebase as small and lightweight as possible, even as your project grows. Thus you should:
Cách tốt nhất để đối phó là giữ cho cơ sở mã của bạn càng nhỏ và nhẹ càng tốt, ngay cả khi dự án của bạn phát triển. Vì vậy, bạn nên:

- Create as much generic “utility” code as possible to remove duplicated code. (See Chapter 10, Extracting Unrelated Subproblems.)
- Remove unused code or useless features. (See the following sidebar.)
- Keep your project compartmentalized into disconnected subprojects.
- Generally, be conscious of the “weight” of your codebase. Keep it light and nimble
- Tạo càng nhiều mã “tiện ích” chung chung càng tốt để loại bỏ mã trùng lặp. (Xem Chương 10, Trích xuất các vấn đề con không liên quan.)
- Loại bỏ mã không sử dụng hoặc các tính năng vô dụng. (Xem thanh bên sau.)
- Giữ cho dự án của bạn được phân chia thành các tiểu dự án bị ngắt kết nối.
- Nói chung, hãy chú ý đến “trọng lượng” của codebase của bạn. Giữ cho nó nhẹ nhàng và nhanh nhẹn
-
> REMOVING UNUSED CODE
> Gardeners often prune plants to keep them alive and growing. Similarly, it’s a good idea to prune any unused code that’s getting in the way

> Once code is written, coders are often reluctant to delete it, because it represents a lot of real work. To delete it would mean admitting that the time spent on it was wasted. Well, get over it! This is a creative field—photographers, writers, and filmmakers don’t keep all of their work, either
> Deleting isolated functions is easy, but sometimes “unused code” is actually woven throughout your project, unbeknownst to you. Here are some examples:
> - You originally designed your system to handle international filenames, and now the code is littered with conversion code. However, that code isn’t fully functional, and your app is never used with international filenames anyhow. Why not remove this functionality?
> - ou wanted your program to work even if the system ran out of memory, so you have lots of clever logic that tries to recover from out-of-memory situations. It was a good idea, but in practice, when the system runs out of memory, your program just becomes an unstable zombie anyway—all the core features are unusable, and it’s one mouse click away from dying.
> Why not just terminate the program with a simple “The system is out of memory, sorry” and remove all this out-of-memory code?

> XÓA MÃ LIÊN KẾT
> Người làm vườn thường cắt tỉa cây để cây sống và phát triển. Tương tự, bạn nên cắt bỏ bất kỳ mã không sử dụng nào đang cản trở

> Một khi mã được viết, các lập trình viên thường miễn cưỡng xóa nó, bởi vì nó đại diện cho rất nhiều công việc thực tế. Xóa nó có nghĩa là thừa nhận rằng thời gian dành cho nó đã bị lãng phí. Chà, vượt qua nó! Đây là một lĩnh vực sáng tạo — các nhiếp ảnh gia, nhà văn và nhà làm phim không giữ tất cả công việc của họ
> Xóa các chức năng biệt lập rất dễ dàng, nhưng đôi khi “mã không sử dụng” thực sự được thêu dệt trong suốt dự án của bạn mà bạn không biết. Dưới đây là một số ví dụ:
> - Ban đầu, bạn đã thiết kế hệ thống của mình để xử lý các tên tệp quốc tế, và bây giờ mã này có rất nhiều mã chuyển đổi. Tuy nhiên, mã đó không hoạt động đầy đủ và ứng dụng của bạn sẽ không bao giờ được sử dụng với các tên tệp quốc tế. Tại sao không loại bỏ chức năng này?
> - ou muốn chương trình của bạn hoạt động ngay cả khi hệ thống hết bộ nhớ, vì vậy bạn có rất nhiều logic thông minh để cố gắng khôi phục từ các tình huống hết bộ nhớ. Đó là một ý tưởng hay, nhưng trên thực tế, khi hệ thống hết bộ nhớ, chương trình của bạn sẽ trở thành một thây ma không ổn định — tất cả các tính năng cốt lõi đều không thể sử dụng được và chỉ cần một cú nhấp chuột là bạn sẽ chết.
> Tại sao không chỉ kết thúc chương trình bằng một câu đơn giản “Hệ thống hết bộ nhớ, xin lỗi” và xóa tất cả mã hết bộ nhớ này?
## Be Familiar with the Libraries Around You

A lot of the time, programmers just aren’t aware that existing libraries can solve their problem. Or sometimes they’ve forgotten what a library can do. It’s important to know the capabilities of your library code so that you can make use of it.
Đôi khi, các lập trình viên không biết rằng các thư viện hiện có có thể giải quyết vấn đề của họ. Hoặc đôi khi họ quên rằng thư viện có thể làm gì. Điều quan trọng là phải biết các khả năng của mã thư viện của bạn để bạn có thể sử dụng nó.

Here’s a modest suggestion: every once in a while, spend 15 minutes reading the names of all the functions/modules/types in your standard library. These include the C++ Standard Template Library (STL), the Java API, the built-in Python modules, and others.
Đây là một gợi ý khiêm tốn: thỉnh thoảng, hãy dành 15 phút để đọc tên của tất cả các hàm / mô-đun / loại trong thư viện chuẩn của bạn. Chúng bao gồm Thư viện mẫu chuẩn C ++ (STL), API Java, các mô-đun Python tích hợp sẵn và những thứ khác.

The goal isn’t to memorize the whole library. It’s just to get a sense of what’s available, so that next time you’re working on new code you’ll think, “Wait, this sounds similar to something I saw in the API….” We believe doing this work upfront pays off quickly, as you’ll be more inclined to use those libraries in the first place.
Mục tiêu không phải là ghi nhớ toàn bộ thư viện. Nó chỉ là để hiểu những gì có sẵn, để lần sau khi làm việc trên mã mới, bạn sẽ nghĩ, “Chờ đã, điều này nghe tương tự như điều gì đó tôi đã thấy trong API….” Chúng tôi tin rằng thực hiện công việc trả trước này sẽ nhanh chóng mang lại hiệu quả, vì bạn sẽ có xu hướng sử dụng các thư viện đó ngay từ đầu.
### Example: Lists and Sets in Python
Suppose you have a list in Python (like [2,1,2]) and you want a list of the unique elements (in this case, [2,1]). You could implement this task using a dictionary, which has a list of keys that are guaranteed to be unique:
Giả sử bạn có một danh sách bằng Python (như [2,1,2]) và bạn muốn một danh sách các phần tử duy nhất (trong trường hợp này là [2,1]). Bạn có thể thực hiện tác vụ này bằng cách sử dụng từ điển, có danh sách các khóa được đảm bảo là duy nhất:
```python
def unique(elements):
    temp = {}
    for element in elements:
        temp[element] = None # The value doesn't matter.
    return temp.keys()

unique_elements = unique([2,1,2])
```
But instead you can just use the lesser-known set type:
Nhưng thay vào đó, bạn chỉ có thể sử dụng loại tập hợp ít được biết đến hơn:
```
unique_elements = set([2,1,2]) # Remove duplicates
```
This object is iterable, just like a normal list. If you really want a list object again, you can just use:
Đối tượng này có thể lặp lại, giống như một danh sách bình thường. Nếu bạn thực sự muốn một đối tượng danh sách một lần nữa, bạn chỉ có thể sử dụng:
```
unique_elements = list(set([2,1,2])) # Remove duplicates
```
Clearly, set is the right tool for the job here. But if you weren’t aware of the set type, you might produce code like unique() above.
Rõ ràng, set là công cụ phù hợp cho công việc ở đây. Nhưng nếu bạn không biết về loại tập hợp, bạn có thể tạo mã như unique () ở trên.

### Why Reusing Libraries Is Such a Win
A commonly cited statistic is that the average software engineer produces ten shippable lines of code a day. When programmers first hear this, they balk in disbelief—“Ten lines of code? I can write that in a minute!”
Một thống kê thường được trích dẫn là một kỹ sư phần mềm trung bình tạo ra mười dòng mã có thể chuyển đổi mỗi ngày. Khi các lập trình viên lần đầu tiên nghe thấy điều này, họ không tin nổi— “Mười dòng mã? Tôi có thể viết nó trong một phút! ”

The key word is shippable. Each line of code in a mature library represents a fair amount of design, debugging, rewriting, documenting, optimizing, and testing. Any line of code that’s survived this Darwinian process is very valuable. This is why reusing libraries is such a win, in both saving time and having less code to write.
Từ khóa là có thể chuyển nhượng được. Mỗi dòng mã trong một thư viện trưởng thành đại diện cho một lượng lớn thiết kế, gỡ lỗi, viết lại, lập tài liệu, tối ưu hóa và thử nghiệm. Bất kỳ dòng mã nào còn tồn tại trong quá trình Darwin này đều rất có giá trị. Đây là lý do tại sao sử dụng lại các thư viện là một chiến thắng, vừa tiết kiệm thời gian vừa ít phải viết mã hơn.

## Example: Using Unix Tools Instead of Coding

When a web server frequently returns 4xx or 5xx HTTP response codes, it’s a sign of a potential problem (4xx being a client error; 5xx being a server error). So we wanted to write a program that parses a web server’s access logs and determines which URLs are causing the most errors.
Khi máy chủ web thường xuyên trả về mã phản hồi HTTP 4xx hoặc 5xx, đó là dấu hiệu của sự cố tiềm ẩn (4xx là lỗi máy khách; 5xx là lỗi máy chủ). Vì vậy, chúng tôi muốn viết một chương trình phân tích nhật ký truy cập của máy chủ web và xác định URL nào gây ra nhiều lỗi nhất.

The access logs typically look something like this:
Nhật ký truy cập thường trông giống như sau:
```
1.2.3.4 example.com [24/Aug/2010:01:08:34] "GET /index.html HTTP/1.1" 200 ...
2.3.4.5 example.com [24/Aug/2010:01:14:27] "GET /help?topic=8 HTTP/1.1" 500 ...
3.4.5.6 example.com [24/Aug/2010:01:15:54] "GET /favicon.ico HTTP/1.1" 404 ...
...
```
Generally, they contain lines of this form:
Nói chung, chúng chứa các dòng dạng này:
```
browser-IP host [date] "GET /url-path HTTP/1.1" HTTP-response-code ...
```
Writing a program to find the most common url-paths with 4xx or 5xx response codes might easily take 20 lines of code in a language like C++ or Java.
Việc viết một chương trình để tìm các đường dẫn url phổ biến nhất với mã phản hồi 4xx hoặc 5xx có thể dễ dàng mất 20 dòng mã trong một ngôn ngữ như C ++ hoặc Java.

Instead, in Unix, you can type this command line:
Thay vào đó, trong Unix, bạn có thể nhập dòng lệnh này:
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

> Phiêu lưu, phấn khích — một Jedi không thèm muốn những thứ này.
> —Yoda

Chương này nói về việc viết càng ít mã mới càng tốt. Mỗi dòng mã mới cần được kiểm tra, ghi lại và duy trì. Hơn nữa, càng nhiều mã trong cơ sở mã của bạn, thì nó càng “nặng” và càng khó phát triển.

Bạn có thể tránh viết các dòng mã mới bằng cách:

- Loại bỏ các tính năng không cần thiết khỏi sản phẩm của bạn và không khai thác quá mức
- Suy nghĩ lại các yêu cầu để giải quyết phiên bản dễ nhất của vấn đề mà vẫn hoàn thành công việc
- Làm quen với các thư viện tiêu chuẩn bằng cách đọc định kỳ qua toàn bộ API của chúng




















