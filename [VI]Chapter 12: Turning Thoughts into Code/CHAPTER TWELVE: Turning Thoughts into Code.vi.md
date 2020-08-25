# CHAPTER TWELVE: Turning Thoughts into Code

![](https://i.imgur.com/Caz6hHb.png)

> You do not really understand something unless you can explain it to your grandmother.
> —Albert Einstein
>

> Bạn không thực sự hiểu điều gì cho tới khi bạn giải thích nó cho bà của bạn
> —Albert Einstein
>

When explaining a complex idea to someone, it's easy to confuse them with all the little details. It's a valuable skill to be able to explain an idea “in plain English,” so that someone less knowledgeable than you can understand. It requires distilling an idea down to the most important concepts. Doing this not only helps the other person understand but also helps you think about your own ideas more clearly.

Khi giải thích một ý tưởng phức tạp cho mọt ai đó, rất dễ nhầm lẫn chúng với các chi tiết nhỏ. Nó là một ký năng đang giá để giải thích một ý tưởng trong tiếng anh, để người ít hiểu biết hơn bạn có thể hiểu. Nó đòi hỏi phải chắt lọc một ý tưởng cho đến những khai niệm quan trọng nhất. Việc làm này không chỉ giúp đối phương hiểu mà còn giúp bạn suy nghĩ rõ ràng hơn về ý tưởng của mình.

The same skill should be used when “presenting” code to your reader. We take the view that source code is the primary way to explain what a program is doing. So the code should be written “in plain English.”

Kỹ năng tương tự cũng nên được sử dụng khi “trình bày” mã cho người đọc của bạn. Chúng tôi cho rằng mã nguồn là cách chính để giải thích chương trình đang làm gì. Vì vậy, mã phải được viết "bằng tiếng Anh đơn giản."

In this chapter, we’ll use a simple process that can help you code more clearly:
    1. Describe what code needs to do, in plain English, as you would to a colleague.
    2. Pay attention to the key words and phrases used in this description.
    3. Write your code to match this description.

Trong chương này, chúng tôi sẽ sử dụng một quy trình đơn giản có thể giúp bạn viết mã rõ ràng hơn:
     1. Mô tả những gì mã cần làm, bằng tiếng Anh đơn giản, như bạn làm với đồng nghiệp.
     2. Chú ý đến các từ và cụm từ chính được sử dụng trong mô tả này.
     3. Viết mã của bạn để phù hợp với mô tả này.
## Describing Logic Clearly

Here is a snippet of code from a web page in PHP. This code is at the top of a secured page. It checks whether the user is authorized to see the page, and if not, immediately returns a page telling the user she is not authorized:

Đây là một đoạn code php. Đầu đoạn code này là một đoạn bảo mật. Nó kiểm tra xem người dùng có được phép xem trang không và nếu không, nó sẽ lập tức trả về một trang cho người dùng để thông báo việc này.

```
$is_admin = is_admin_request();
if ($document) {
    if (!$is_admin && ($document['username'] != $_SESSION['username'])) {
        return not_authorized();
    }
} else {
    if (!$is_admin) {
        return not_authorized();
    }
}

// continue rendering the page ...
```

There’s quite a bit of logic in this code. As you saw in Part II, Simplifying Loops and Logic, large logic trees like this aren’t easy to understand. The logic in this code can probably be simplified, but how? Let’s start by describing the logic in plain English:

Có khá nhiều logic trong đoạn mã này. Như bạn đã thấy trong Phần II, Đơn giản hóa vòng lặp và lôgic, những cây lôgic lớn như thế này không dễ hiểu. Logic trong đoạn mã này có thể được đơn giản hóa, nhưng làm thế nào? Hãy bắt đầu bằng cách mô tả logic bằng tiếng Anh đơn giản:
```
There are two ways you can be authorized:
1) you are an admin
2) you own the current document (if there is one)
Otherwise, you are not authorized.

Có hai cách để bạn có thể được ủy quyền:
1) bạn là quản trị viên
2) bạn sở hữu tài liệu hiện tại (nếu có)
Nếu không, bạn không được ủy quyền.
```
Here is an alternative solution inspired by this description:
Đây là một giải pháp thay thế được lấy cảm hứng từ mô tả này:
```
if (is_admin_request()) {
    // authorized
} elseif ($document && ($document['username'] == $_SESSION['username'])) {
    // authorized
} else {
    return not_authorized();
}

// continue rendering the page ...
```
This version is slightly unusual because it has two empty bodies. But the code is smaller, and the logic is simpler, because there is no negation. (The previous solution had three “nots.”) The bottom line is that it’s easier to understand.

Phiên bản này hơi khác thường vì nó có hai phần thân trống. Nhưng mã nhỏ hơn, và logic đơn giản hơn, vì không có phủ định. (Giải pháp trước đó có ba chữ “không”.) Điểm mấu chốt là nó dễ hiểu hơn.
## Knowing Your Libraries Helps
We once had a website that included a “tips box” that showed the user helpful suggestions like:
Chúng tôi đã từng có một trang web bao gồm một "hộp mẹo" hiển thị cho người dùng các đề xuất hữu ích như:
```
Tip: Log in to see your past queries. [Show me another tip!]
```

There were a few dozen tips, and all of them were hidden inside the HTML:
Có một vài mẹo và tất cả chúng đều được ẩn bên trong HTML:

```htmlmixed=
<div id="tip-1" class="tip">Tip: Log in to see your past queries.</div>
<div id="tip-2" class="tip">Tip: Click on a picture to see it close up.</div>
...
```
When a user visited the page, one of these divs was randomly made visible, and the rest stayed hidden.
Khi người dùng truy cập trang, một trong những div này được hiển thị ngẫu nhiên và phần còn lại được ẩn.

If the “Show me another tip!" link was clicked, it cycled to the next tip. Here is some code to implement that feature using the jQuery JavaScript library:
Nếu liên kết “Chỉ cho tôi một mẹo khác!” Được nhấp vào, liên kết này sẽ chuyển sang mẹo tiếp theo. Đây là một số mã để triển khai tính năng đó bằng thư viện JavaScript jQuery:
```javascript=
var show_next_tip = function () {
    var num_tips = $('.tip').size();
    var shown_tip = $('.tip:visible');

    var shown_tip_num = Number(shown_tip.attr('id').slice(4));
    if (shown_tip_num === num_tips) {
        $('#tip-1').show();
    } else {
        $('#tip-' + (shown_tip_num + 1)).show();
    }
    shown_tip.hide();
};
```

This code is okay. But it can be made better. Let’s start by describing, in words, what this code is trying to do:
Mã này là được. Nhưng nó có thể được làm tốt hơn. Hãy bắt đầu bằng cách mô tả, bằng lời, đoạn mã này đang cố gắng thực hiện:
```
Find the currently visible tip and hide it.
Then find the next tip after it and show that.
If we've run out of tips, cycle back to the first tip.

Tìm mẹo hiện đang hiển thị và ẩn nó.
Sau đó, tìm mẹo tiếp theo sau nó và hiển thị điều đó.
Nếu chúng tôi đã sử dụng hết mẹo, hãy quay lại mẹo đầu tiên.
```
Based on this description, here’s another solution:
Dựa trên mô tả này, đây là một giải pháp khác:
```javascript=
var show_next_tip = function () {
    var cur_tip = $('.tip:visible').hide(); // find the currently visible tip and hide it
    var next_tip = cur_tip.next('.tip'); // find the next tip after it
    if (next_tip.size() === 0) { // if we've run out of tips,
        next_tip = $('.tip:first'); // cycle back to the first tip
    }
    next_tip.show(); // show the new tip
};
```
This solution contains fewer lines of code and doesn’t have to manipulate integers directly. It’s more aligned with how a person would think about the code.
Giải pháp này chứa ít dòng mã hơn và không phải thao tác trực tiếp với số nguyên. Nó phù hợp hơn với cách một người nghĩ về mã.

In this case, it helped that jQuery has a .next() method we could use. Part of writing succinct code is being aware of what your library has to offer.
Trong trường hợp này, nó đã giúp jQuery có một phương thức .next () mà chúng ta có thể sử dụng. Một phần của việc viết mã ngắn gọn là nhận thức được những gì thư viện của bạn cung cấp.
## Applying This Method to Larger Problems

The previous examples have applied our process to small blocks of code. In the next example, we’ll apply it to a larger function. As you’ll see, this method can help you break down your code by helping you identify what pieces you can break away.

Các ví dụ trước đã áp dụng quy trình của chúng tôi cho các khối mã nhỏ. Trong ví dụ tiếp theo, chúng tôi sẽ áp dụng nó cho một hàm lớn hơn. Như bạn sẽ thấy, phương pháp này có thể giúp bạn chia nhỏ mã của mình bằng cách giúp bạn xác định những phần bạn có thể chia nhỏ.

Imagine we have a system that records stock purchases. Each transaction has four pieces of data:
- time (a precise date and time of the purchase)
- ticker_symbol (e.g., GOOG)
- price (e.g., $600)
- number_of_shares (e.g., 100)
Hãy tưởng tượng chúng ta có một hệ thống ghi lại các giao dịch mua cổ phiếu. Mỗi giao dịch có bốn phần dữ liệu:
- thời gian (ngày và giờ mua hàng chính xác)
- ticker_symbol (ví dụ: GOOG)
- giá (ví dụ: 600 đô la)
- number_of_shares (ví dụ: 100)
For some strange reason, the data is spread across three separate database tables, as illustrated here. In each database, the time is the unique primary key.
Vì một số lý do kỳ lạ, dữ liệu được trải rộng trên ba bảng cơ sở dữ liệu riêng biệt, như được minh họa ở đây. Trong mỗi cơ sở dữ liệu, thời gian là khóa chính duy nhất.
![](https://i.imgur.com/i09gYMe.png)

Now we need to write a program to join the three tables back together (as an SQL JOIN operation would do). This step should be easy because the rows are all sorted by time, but unfortunately some of the rows are missing. You want to find all the rows where all three times match up, and ignore any rows that can’t be lined up, as shown in the previous illustration.
Bây giờ chúng ta cần viết một chương trình để nối ba bảng lại với nhau (như một phép toán SQL JOIN sẽ làm). Bước này sẽ dễ dàng vì các hàng đều được sắp xếp theo thời gian, nhưng rất tiếc là một số hàng bị thiếu. Bạn muốn tìm tất cả các hàng mà cả ba thời điểm đều trùng khớp và bỏ qua bất kỳ hàng nào không thể xếp hàng, như thể hiện trong hình minh họa trước.

Here is some Python code that finds all the matching rows:
Đây là một số mã Python tìm thấy tất cả các hàng phù hợp:
```python

```
This example code works, but there’s a lot going on with how the loop skips over unmatched rows. Some warning flags might have gone off in your head: Could this miss any rows? Might it read past the end-of-stream for any of the iterators?
Mã ví dụ này hoạt động, nhưng có rất nhiều điều xảy ra với cách vòng lặp bỏ qua các hàng chưa khớp. Một số cờ cảnh báo có thể đã xuất hiện trong đầu bạn: Điều này có thể bỏ sót hàng nào không? Nó có thể đọc quá cuối luồng cho bất kỳ trình vòng lặp nào không?
So how can we make it more readable?
Vì vậy, làm thế nào chúng ta có thể làm cho nó dễ đọc hơn?
```python=
def PrintStockTransactions():
    stock_iter = db_read("SELECT time, ticker_symbol FROM ...")
    price_iter = ...
    num_shares_iter = ...

    # Iterate through all the rows of the 3 tables in parallel.
    while stock_iter and price_iter and num_shares_iter:
        stock_time = stock_iter.time
        price_time = price_iter.time
        num_shares_time = num_shares_iter.time

        # If all 3 rows don't have the same time, skip over the oldest row
        # Note: the "<=" below can't just be "<" in case there are 2 tied-oldest.
        if stock_time != price_time or stock_time != num_shares_time:
            if stock_time <= price_time and stock_time <= num_shares_time:
                stock_iter.NextRow()
            elif price_time <= stock_time and price_time <= num_shares_time:
                price_iter.NextRow()
            elif num_shares_time <= stock_time and num_shares_time <= price_time:
                num_shares_iter.NextRow()
            else:
                assert False # impossible
            continue

        assert stock_time == price_time == num_shares_time

        # Print the aligned rows.
        print "@", stock_time,
        print stock_iter.ticker_symbol,
        print price_iter.price,
        print num_shares_iter.number_of_shares

        stock_iter.NextRow()
        price_iter.NextRow()
        num_shares_iter.NextRow()
```

### An English Description of the Solution

Once again, let’s step back and describe in plain English what we’re trying to do:
Một lần nữa, hãy quay lại và mô tả bằng tiếng Anh đơn giản về những gì chúng tôi đang cố gắng thực hiện:
```
We are reading three row iterators in parallel.
Whenever the rows' times don't line up, advance the rows so they do line up.
Then print the aligned rows, and advance the rows again.
Keep doing this until there are no more matching rows left.

Chúng tôi đang đọc ba trình lặp hàng song song.
Bất cứ khi nào thời gian của các hàng không xếp hàng, hãy tiến các hàng để chúng thẳng hàng.
Sau đó, in các hàng đã căn chỉnh và tiến lại các hàng.
Tiếp tục làm điều này cho đến khi không còn hàng nào phù hợp nữa.
```
Looking back at the original code, the messiest part was the block dealing with “advance the rows so they do line up.” To present the code more clearly, we can extract all that messy logic into a new function named AdvanceToMatchingTime().

Here’s a new version of the code, making use of this new function:

Nhìn lại mã ban đầu, phần lộn xộn nhất là khối xử lý "nâng cao các hàng để chúng xếp hàng." Để trình bày mã rõ ràng hơn, chúng ta có thể trích xuất tất cả logic lộn xộn đó vào một hàm mới có tên AdvanceToMatchingTime ().

Đây là phiên bản mới của mã, sử dụng chức năng mới này:

```python=
def PrintStockTransactions():
    stock_iter = ...
    price_iter = ...
    num_shares_iter = ...

    while True:
        time = AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter)
        if time is None:
            return

        # Print the aligned rows.
        print "@", time,
        print stock_iter.ticker_symbol,
        print price_iter.price,
        print num_shares_iter.number_of_shares

        stock_iter.NextRow()
        price_iter.NextRow()
        num_shares_iter.NextRow()
```

As you can see, this code is much easier to understand, as we’ve hidden away all the dirty details of lining up the rows.
Như bạn có thể thấy, mã này dễ hiểu hơn nhiều, vì chúng tôi đã ẩn tất cả các chi tiết bẩn của việc sắp xếp các hàng.
### Applying the Method Recursively

It’s easy to imagine how you would write AdvanceToMatchingTime()—in the worst case, it would look very similar to that ugly block of code from the first version:
Thật dễ dàng để tưởng tượng bạn sẽ viết AdvanceToMatchingTime () như thế nào - trong trường hợp xấu nhất, nó sẽ trông rất giống với khối mã xấu xí đó từ phiên bản đầu tiên:
```python=
def AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter):
    # Iterate through all the rows of the 3 tables in parallel.
    while stock_iter and price_iter and num_shares_iter:
        stock_time = stock_iter.time
        price_time = price_iter.time
        num_shares_time = num_shares_iter.time

        # If all 3 rows don't have the same time, skip over the oldest row
        if stock_time != price_time or stock_time != num_shares_time:
            if stock_time <= price_time and stock_time <= num_shares_time:
              stock_iter.NextRow()
            elif price_time <= stock_time and price_time <= num_shares_time:
                price_iter.NextRow()
            elif num_shares_time <= stock_time and num_shares_time <= price_time:
                num_shares_iter.NextRow()
            else:
                assert False # impossible
            continue
            assert stock_time == price_time == num_shares_time
            return stock_time
```

But let’s improve that code by applying our method to AdvanceToMatchingTime() as well. Here’s a description of what this function needs to do:
Nhưng chúng ta hãy cải thiện mã đó bằng cách áp dụng phương pháp của chúng tôi cho AdvanceToMatchingTime (). Dưới đây là mô tả về những gì chức năng này cần thực hiện:
```
Look at the times of each current row: if they're aligned, we're done.
Otherwise, advance any rows that are "behind."
Keep doing this until the rows are aligned (or one of the iterators has ended).

Nhìn vào thời gian của mỗi hàng hiện tại: nếu chúng được căn chỉnh, chúng ta đã hoàn tất.
Nếu không, hãy nâng cao bất kỳ hàng nào "phía sau".
Tiếp tục làm điều này cho đến khi các hàng được căn chỉnh (hoặc một trong các trình vòng lặp đã kết thúc).
```

This description is a lot clearer and more elegant than the previous code. One thing to notice is that the description never mentions stock_iter or other details specific to our problem. This means we can also rename the variables to be simpler and more general. Here’s the resulting code:

Mô tả này rõ ràng và trang nhã hơn rất nhiều so với mã trước đó. Một điều cần lưu ý là mô tả không bao giờ đề cập đến stock_iter hoặc các chi tiết khác cụ thể cho vấn đề của chúng tôi. Điều này có nghĩa là chúng ta cũng có thể đổi tên các biến để đơn giản hơn và tổng quát hơn. Đây là mã kết quả:

```python
def AdvanceToMatchingTime(row_iter1, row_iter2, row_iter3):
    while row_iter1 and row_iter2 and row_iter3:
        t1 = row_iter1.time
        t2 = row_iter2.time
        t3 = row_iter3.time

        if t1 == t2 == t3:
            return t1

        tmax = max(t1, t2, t3)

        # If any row is "behind," advance it.
        # Eventually, this while loop will align them all.
        if t1 < tmax: row_iter1.NextRow()
        if t2 < tmax: row_iter2.NextRow()
        if t3 < tmax: row_iter3.NextRow()
    return None # no alignment could be found
```

As you can see, this code is a lot clearer than before. The algorithm became simpler, and now there are fewer tricky comparisons. And we used short names like t1 and no longer had to think about the specific database columns involved.
Như bạn có thể thấy, mã này rõ ràng hơn trước rất nhiều. Thuật toán trở nên đơn giản hơn và bây giờ có ít phép so sánh phức tạp hơn. Và chúng tôi đã sử dụng các tên ngắn như t1 và không còn phải suy nghĩ về các cột cơ sở dữ liệu cụ thể liên quan.
## Summary

This chapter discussed the simple technique of describing your program in plain English and using that description to help you write more natural code. This technique is deceptively simple, but very powerful. Looking at the words and phrases used in your description can also help you identify which subproblems to break off.
Chương này thảo luận về kỹ thuật đơn giản để mô tả chương trình của bạn bằng tiếng Anh đơn giản và sử dụng mô tả đó để giúp bạn viết mã tự nhiên hơn. Kỹ thuật này có thể nói là đơn giản, nhưng rất mạnh mẽ. Xem xét các từ và cụm từ được sử dụng trong mô tả của bạn cũng có thể giúp bạn xác định vấn đề phụ nào cần giải quyết.

But this process of “saying things in plain English” is applicable outside of just writing code. For example, one college computer lab policy states that when a student needs help debugging his program, he first has to explain the problem to a dedicated teddy bear in the corner of the room. Surprisingly, just describing the problem aloud can often help the student figure out a solution. This technique is called “rubber ducking.”
Nhưng quá trình “nói mọi thứ bằng tiếng Anh thuần túy” này có thể áp dụng ngoài việc viết mã. Ví dụ: chính sách phòng máy tính của một trường đại học quy định rằng khi một sinh viên cần trợ giúp gỡ lỗi chương trình của mình, trước tiên anh ta phải giải thích vấn đề với một con gấu bông chuyên dụng ở góc phòng. Đáng ngạc nhiên là chỉ cần mô tả vấn đề to thường có thể giúp học sinh tìm ra giải pháp. Kỹ thuật này được gọi là “dúm cao su”.

Another way to look at it is this: if you can't describe the problem or your design in words, something is probably missing or undefined. Getting a program (or any idea) into words can really force it into shape.
Một cách khác để xem xét vấn đề này là: nếu bạn không thể mô tả vấn đề hoặc thiết kế của mình bằng lời, có thể điều gì đó bị thiếu hoặc chưa được xác định. Việc chuyển một chương trình (hoặc bất kỳ ý tưởng nào) thành lời có thể thực sự khiến nó thành hình.















