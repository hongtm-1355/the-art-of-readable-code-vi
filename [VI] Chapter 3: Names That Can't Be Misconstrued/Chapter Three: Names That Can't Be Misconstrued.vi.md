# Chapter Three: Names That Can't Be Misconstrued
![](https://i.imgur.com/pynFtTu.png)

Trong chương trước, chúng đã đã đề cập về việc mang thông tin vào tên biến. Trong chương này, chúng ta sẽ tập trung vào một vấn đề khác: những tên biến có thể gây hiểu nhầm

> Ý tưởng chính
> Hãy xem lại tên biến của bạn bằng cách tự hỏi: "Người khác có thể dịch tên biến này sang nghĩa nào khác?"
>

Hãy có gắng để sáng tạo ở đây, tìm kiếm những "cách dịch sai" một cách chủ động. Bước này sẽ giúp bạn phát hiện ra những tên biến không rõ ràng và sửa đổi chúng

Về những ví dụ trong chương này, chúng ta sẽ "bóc phốt" những tên biến như vậy và chọn những cái tên thích hợp hơn cho chúng.

## Ví dụ: `Filter()`

Giả sửa bạn đang viết code để lấy ra một tập dữ liệu trong database:

```
results = Database.all_objects.filter("year <= 2011")
```
Giờ hãy tự hỏi: `results` chứa cái gì?
- Những **object** mà năm của chúng `<= 2011`?
- Những **object** mà năm của chúng không phải `<= 2011`?

Vấn đề ở đây, `filter` là một từ không rõ ràng. Nó có nghĩa là "lấy ra" hay là "không lấy ra"? Tốt nhất là tránh những tên biến kiểu như `filter` (những động từ chung chung như "lọc") bởi vì chúng rất dễ bị hiểu sai.
Nếu bạn muốn "lấy ra", tên biến hay hơn đó là `select()`, còn "không lấy ra" thì chọn `exclude()`.

## Ví dụ: `Clip(text, length)`
Giả sử như bạn có một hàm dùng để cắt nội dung của một đoạn văn bản:

```python
# Cuts off the end of the text, and appends "..."
def Clip(text, length):
...
```

Sẽ có hai cách bạn có thể hình dung ra cách hoạt động của hàm `Clip()` này:
- Nó sẽ xóa `text` một đoạn `length`, tính từ cuối đoạn văn bản
- Nó sẽ cắt `text `đến độ dài `length`

Cách thứ hai có vẻ như là đúng hơn, những bạn sẽ không thể biết chắc chắn được. Tốt hơn là bạn nên đổi tên hàm thành `Truncate(text, length)` thay vì để người code sau cằn nhằn "code thối thế ..."

Thực ra, nếu tham số `length` đặt là `max_length` thì sẽ còn rõ ràng hơn.
Vẫn chưa hết, `max_length` vẫn có thể hiểu là:
- Số lượng `byte`
- Số lượng `character`
- Số lượng `word`
Ở chương trước, đây là trường hợp mà đơn vị tính nên gắn liền với tên biến. Trong trường hợp này, bạn nên đổi `max_length` thành `max_chars`.

## Sử dụng "max", "min" cho biến thuộc dạng `limit`

Giả sử một ứng dụng giỏ hàng của bạn muốn ngăn không cho người dùng mua 10 sản phẩm cùng một lúc:
```
CART_TOO_BIG_LIMIT = 10

if shopping_cart.num_items() >= CART_TOO_BIG_LIMIT:
    Error("Too many items in cart.")
```
Đoạn code này có thể "sửa trong một nốt nhạc" bằng cách đổi `>=` thành `>`:
```
if shopping_cart.num_items() > CART_TOO_BIG_LIMIT:
```
(hoặc đổi `CART_TOO_BIG_LIMIT` thành `11`). Nhưng vấn đề cốt lõi là `CART_TOO_BIG_LIMIT` là một biến không rõ ràng, nó có nghĩa là "có thể bằng" hay "không thể bằng" 10?

> LỜI KHUYÊN
> Cách tốt nhất để đặt tên cho một `limit` là thêm `max_` hoặc `min_` lên đầu của tên biến

Trong trường hợp này, tên biến nên đặt là `MAX_ITEMS_IN_CART`. Code sẽ đơn giản và dễ hiểu hơn:

```
MAX_ITEMS_IN_CART = 10

if shopping_cart.num_items() > MAX_ITEMS_IN_CART:
    Error("Too many items in cart.")
```

## Dùng `first` và `last` cho những `inclusive range`

![](https://i.imgur.com/ZasRs15.png)

Dưới đây là một ví dụ mà rất khó để nói là "bao gồm" hay "không bao gồm":
```
print integer_range(start=2, stop=4)
# Đoạn code này sẽ in ra [2,3] hay [2,3,4] (hay cái gì đó khác)?
```
Ở đây, `start` là một cái tên hợp lý, nhưng `stop` lại có thể dịch sang nhiều nghĩa khác nhau.
Những `range` giống như thế này (`range` ở đây bao gồm cả hai đầu mút) thì tốt nhất nên đặt là `first`, `last`. Ví dụ:
```
set.PrintKeys(first="Bart", last="Maggie")
```
Không giống như `stop`, `last` đã có nghĩa là "bao gồm chính nó".
Ngoài ra, `min/max` cũng có thể được sử dụng để chỉ những `inclusive range`, chỉ đảm bảo rằng nó "nghe thuyết phục" trong trường hợp đó.

## Prefer begin and end for Inclusive/Exclusive Ranges

![](https://i.imgur.com/4PDMKAu.png)

Trong thực tế, có những trường hợp sử dụng `inclusive range` sẽ dễ hơn `exclusive range` và ngược lại. Ví dụ, nếu bạn muốn in ra tất cả những sự kiện trong ngày 16 tháng 10 thì viết:
```
# Exclusive way
PrintEventsInRange("OCT 16 12:00am", "OCT 17 12:00am")
```
sẽ dễ hơn:
```
# Inclusive way
PrintEventsInRange("OCT 16 12:00am", "OCT 16 11:59:59.9999pm")
```
Vậy tên tham số nên đặt là gì cho hay? Quy ước thông thường cho `inclusive/exclusive range` là `begin/end`
Nhưng "end" thì có một chút không rõ ràng. Ví dụ, trong câu: "I’m at the end of the book" (Tôi đã đọc đến (trang) cuối của cuốn sách), từ "end" ở đây có nghĩa là "đã bao gồm trang cuối". Vì vậy, dùng "end" là chưa thực sự chính xác 100%. Rất tiếc là Tiếng Anh không có từ nào hàm ý "just past the last value" (vừa hoàn thành xong điều vừa nói) (và có vẻ như tiếng Việt cũng vậy :rofl:)

Nhưng bởi vì `begin/end` thậm chí đã trở thành một thành ngữ (ít nhất thì nó đã được sử dụng trong thư viện của C++ và khi cắt mảng thì người ta cũng dùng từ này) nên đây sẽ là lựa chọn tốt nhất.

## Đặt tên biến "boolean"

Khi đặt tên cho biến "boolean" hoặc "function" trả về giá trị "boolean", hãy chắc rằng "true" và "false" thực sự có nghĩa.
Dưới đây là một ví dụ:
```
bool read_password = true;
```
Có hai cách để hiểu đoạn code này:
- Chúng ta cần phải "read password"
- "password" đã được "read"
Trong trường hợp này, tốt hơn hết là tránh từ "read" và đặt tên là `need_password` hoặc `user_is_authenticated`

Thông thường, những từ như "is", "has", "can" hoặc "should" sẽ khiến cho biến "boolean" trở nên rõ ràng hơn.
Ví dụ, một "function" được đặt tên là `SpaceLeft()` nghe giống như nó sẽ trả về một con số định lượng nào đó. Nếu như nó trả về một giá trị "boolean" thì tốt hơn nên đặt là `HasSpaceLeft()`.
Cuối cùng, bạn nên tránh đặt tên có nghĩa phủ định. Ví dụ, thay vì:
```
bool disable_ssl = false;
```
thì sẽ dễ hiểu và súc tích hơn nếu bạn viết:
```
bool use_ssl = true;
```
## Đáp ứng kì vọng của coder
Trong một số trường hợp, vẫn có những từ có thể gây hiểu nhầm mặc dù ý của bạn lại không phải như vậy bởi vì định kiến của người code sau. Tốt hơn hết là bạn nên "tặc lưỡi" cho qua và đổi lại chúng.
### Ví dụ: `get*()`
Rất nhiều lập trình viên thường quy ước rằng những "method" bắt đầu bằng "get" có nghĩa là "lightweight accessors" và chỉ đơn giản là chúng trả về một giá trị "internal" nào đó. Không tuân theo quy ước này có thể dẫn đến những hiều nhầm.

Dưới đây là một ví dụ "không được làm" trong Java:
```
public class StatisticsCollector {
    public void addSample(double x) { ... }
        public double getMean() {
            // Iterate through all samples and return total / num_samples
        }
        ...
    }
```
Ở đây, hàm `getMean()` sẽ lặp qua tất cả dữ liệu và trả về kết quả. Tuy nhiên, nó có thể sẽ tốn rất nhiều tài nguyên nếu như có nhiều dữ liệu. Những coder sau có thể sử dụng `getMean()` mà không nghi ngờ gì.

Thay vào đó, "method" này nên được đổi tên thành `computeMean()` vì nghe nó có vẻ như sẽ phức tạp và tốn nhiều tài nguyên hơn. (Bạn vẫn nên sửa lại hàm này để nó "nhẹ" hơn)

### Ví dụ: `list::size()`

Dưới đây là một ví dụ trong thư viện của C++. Đoạn code dưới đây là nguyên nhân của một "bug" mà để tìm ra nó thì giống như "mò kim đáy bể" khiến cho những server của chúng tôi chậm như rùa bò:
```
void ShrinkList(list<Node>& list, int max_size) {
    while (list.size() > max_size) {
        FreeNode(list.back());
        list.pop_back();
    }
}
```
"Bug" ở đây là người code không biết rằng `list.size()` là một phép tính có độ phức tạp `O(n)`, nó đếm "linked list" qua từng "node" một, thay vì chỉ cần trả về kết quả đã tính toán trước đó. Điều này khiến cho hàm `ShrinkList()` trở thành một phép tính có độ phức tạp `O(n2)`

Về mặt kĩ thuật, đoạn code này đúng và trên thực tế nó đã vượt qua được tất cả các "unit test case" của chúng tôi. Nhưng khi `ShrinkList()` được gọi với một "list" có khoảng một triệu phần tử, nó đã tốn hơn một giờ để hoàn thành!

Có thể bạn nghĩ rằng: "Đấy là lỗi của tester, nên đọc document kĩ hơn". Điều đó đúng, nhưng trên thực tế việc hàm `list.size()` chạy một khoảng thời gian lớn như thế là một điều rất bất ngờ. Tất cả những thư viện khác của C++ đều có hàm `size()` với thời gian chạy tuyến tính.

Nếu như `size()` được đổi thành `countSize()` hoặc `countElements()`, lỗi tương tự đã ít bị xảy ra. Người viết code muốn đặt tên hàm là `size()` để phù hợp với cả "vector" và "map". Nhưng vì lẽ đó, những người code đã cho rằng đó là một phép tính nhanh. May thay, hàm `size()` trong phiên bản mới nhất của C++ giờ đã có độ phức tạp `O(1)`.

> AI LÀ PHÙ THỦY?
> Một thời gian về trước. một trong những tác giả của cuốn sách này đã cài đặt hệ điều hành OpenBSD. Trong quá trình định dạng lại ổ đĩa, một menu phức tạp đã hiện lên, hỏi về "disk parameters". Một trong những option đó là "Wizard mode". Anh ấy đã tin rằng đây là một option thân thiện với người dùng và chọn nó. Bất ngờ thay, option đó đã đưa trình cài đặt vào một màn hình cài đặt cấp thấp chờ người dùng nhập những câu lệnh định dạng ổ đĩa. Rõ ràng "wizard" có nghĩa là bạn là phù thủy!

## Ví dụ: Lựa chọn giữa những cái tên xuất sắc

Khi chọn giữa những cái tên hay, bạn có thể sẽ có nhiều lựa chọn để cân nhắc. Hãy lựa chọn những cái tên hợp lý nhất trong đầu của bạn. Ví dụ dưới đây sẽ minh họa quá trình lựa chọn khắc nghiệt này.

Những trang web có lượng truy cập cao thường sử dụng những file "config" để xem xem liệu rằng thay đổi đó có giúp tăng trải nghiệm người dùng. Dưới đây là một ví dụ của một file "config" về một thử nghiệm nào đó:
```
experiment_id: 100
description: "increase font size to 14pt"
traffic_fraction: 5%
...
```

Mỗi thử nghiệm được xác định nghĩa bởi khoảng 15 cặp "attribute/value". Thật không may rằng khi bạn định nghĩa là những thử nghiệm giống nhau, bạn sẽ phải copy hầu hết những dòng dưới đây:
```
experiment_id: 101
description: "increase font size to 13pt"
[other lines identical to experiment_id 100]
```
Giả sử như chúng ta muốn sửa tình huống này bằng cách sử dụng lại những thuộc tính của những file "config" trước đó (đây là tính kế thừa). Kết quả là bạn sẽ làm như sau:
```
experiment_id: 101
the_other_experiment_id_I_want_to_reuse: 100
[change any properties as needed]
```
Câu hỏi đặt ra là: "`the_other_experiment_id_I_want_to_reuse` nên đặt là gì cho chuẩn?"
Dưới đây là bốn cái tên có thể cân nhắc:
1. template
2. reuse
3. copy
4. inherit

Cái tên nào nghe cũng hợp lý bởi vì chúng ta là người thêm tính năng này. Nhưng chúng ta phải tưởng tưởng ra người code sau sẽ tưởng tưởng ra như thế nào khi không biết về tính năng này. Giờ hãy cùng phân tích mỗi cái tên, và nghĩ ra những cách mà người khác có thể hiều nhầm.

1. Nếu chúng ta sử dụng "template":
```
experiment_id: 101
template: 100
...
```
"template" có một vài vấn đề. Đầu tiên, nếu nói "I am a template" (tôi là một "template") hoặc "I am using this other template" (tôi đang sử dụng một "template" khác) thì cũng đều không rõ ràng. Thứ hai, "template" (mẫu, đơn) thường có nghĩa là phải điền một cái gì đấy hoặc hoàn thành một cái gì đấy thì mới có thể sử dụng được. Nhìn chung thì "template" là một từ quá mơ hồ để có thể sử dụng trong tình huống này.

2. Thế còn "reuse" thì sao?
```
experiment_id: 101
reuse: 100
...
```
"reuse" là một từ ổn, nhưng người nào đó có thể hiểu rằng: "Thử nghiệm này có thể đã được sử dụng 100 lần". Đổi thành `reuse_id` có thể sẽ ổn, nhưng người ta vẫn có thể hiểu rằng: "100 nghĩa là id của tôi để người khác reuse"

3. Hãy xem xét "copy".
```
experiment_id: 101
copy: 100
...
```
"copy" là một từ tương đối tốt. Nhưng cũng có thể hiểu: "100 có nghĩa là copy cái thể nghiệm này 100 lần" hoặc "đây là bản sao thứ 100 của một thứ gì đó". Để chỉ rõ ràng rằng từ này đề cập đến một bản thử nghiệm khác, chúng ta có thể đổi thành `copy_experiment`. Đây có lẽ là từ tốt nhất đến hiện tại.

4. Giờ hãy xét đến "inherit":
```
experiment_id: 101
inherit: 100
...
```
Từ "inherit" rất quen thuộc với hầu hết lập trình viên và nó được hiểu rằng một điều gì đó sẽ được chỉnh sửa sau khi kế thừa. Với "class inheritance", bạn sẽ lấy ra được tất cả các "method" và thành phần của một "class" và chỉnh sửa chúng. Thậm chí ở đời thực, khi bạn kế thừa tài sản từ một người thân, bạn cũng có thể bán chúng hoặc giữ lại cho riêng mình.

Quay trở lại với bài toán, hãy đổi sao cho người khác hiểu rằng chúng ta đang kế thừa từ một bản thử nghiệm. Chúng ta có thể cải thiện cái tên này thành `inherit_from` hoặc `inherit_from_experiment_id`

Tóm lại, `copy_experiment` và `inherit_from_experiment_id` là hai cái tên tốt nhất, bởi chúng miêu tả một các rõ ràng nhất những gì chúng ta đang làm và cũng ít bị hiểu nhầm nhất

## Tổng kết

Những cái tên tốt nhất là những cái mà không thể bị hiểu nhầm, người đọc code của bạn sẽ hiểu những "tinh hoa" mà bạn muốn gửi gắm. Đáng tiếc là có nhiều từ tiếng Anh rất mơ hồ khi nói về lập trình, ví dụ như "filter", "length", "limit".

Trước khi bạn quyết định chọn một cái tên, hãy tưởng tượng ra những "tao tưởng ..." của đồng nghiệp. Những từ hay nhất sẽ "miễn nhiễm" với sự hiểu nhầm.

Khi định nghĩa giới hạn trên hoặc giới hạn dưới cho một giá trị nào đó, tiền tố `max_` và `min_` là những lựa chọn tốt. "inclusive range" thì sử dụng "first" và "last". Đối với "inclusive/exclusive range" thì "begin" và "end" là tốt nhất vì chúng là thuật ngữ được sử dụng phổ biến nhất.

Khi đặt tên cho biến "boolean", sử dụng những từ như "is" và "has" để chỉ rõ rằng đó là biến "boolean". Tránh sử dụng những từ phủ định.
When naming a boolean, use words like is and has to make it clear that it’s a boolean. Avoid negated terms (e.g., disable_ssl).

Hãy để ý định kiến của người code sau về một số từ. Ví dụ như người ta thường nghĩ rằng `get()` hoặc `size()` là những "lightweight method".
