# CHƯƠNG 5: NHẬN THỨC ĐÚNG VỀ COMMENT

Mục đích của chương này là giúp bạn nên comment code như thế nào. Việc comment đơn giản chỉ là "giải thích code", điều đó đúng nhưng chỉ là phần nổi của tảng băng chìm.

> K E Y I D E A
>
> Mục đích của việc comment là để giúp người đọc có thể hiểu được toàn bộ dụng ý của người viết

Bạn sẽ có nhiều ý tưởng trong đầu khi viết code. Tuy nhiên, khi người khác đọc code của bạn, những ý tưởng đó bị mất đi, tất cả những gì còn lại là những dòng code trước mặt họ.

Trong chương này, chúng tôi sẽ đưa ra nhiều ví dụ khi nào thì nên viết những ý tưởng trong đầu bạn ra. Thay vì tập trung vào những điều khô khan, chúng tôi sẽ tập trung vào những phần thú vị và "nghệ thuật" trong khía cạnh của việc comment.

Chúng tôi đã sắp xếp chương này theo các phần sau:
- Những điều không nên có trong việc comment
- Ghi lại những ý tưởng của bạn trong khi code
- Đặt hoàn cảnh vào vị trí của người đọc để viết chính xác những gì họ cần

## Những điều KHÔNG NÊN có trong việc comment

Việc đọc comment tốn thời gian và những phần comment chiếm một phần trên màn hình. Chính vì thế, comment nên có giá của nó. Vậy đâu là ranh giới giữa một comment tốt và một comment vô nghĩa?

Tất cả nhưng đoạn comment dưới đây đều là vô nghĩa:
```java
// The class definition for Account
class Account {
  public:
  // Constructor
  Account();

  // Set the profit member to a new value
  void SetProfit(double profit);

  // Return the profit from this Account
  double GetProfit();
};
```
Những đoạn comment này đều vô nghĩa bởi vì chúng không cung cấp bất kì thông tin nào giúp người đọc có thể hiểu đoạn code một cách tốt hơn.
> K E Y I D E A
>
> Không nên comment những điều hiển nhiên mà đọc code đã ngay tức khắc có thể hiểu ra.

Mặc dù vậy, từ "ngay tức khắc" ở đây cũng cần phải được xem xét một cách cẩn thận. Hãy xem đoạn comment của đoạn code Python dưới đây:
```python
# remove everything after the second '*'
name = '*'.join(line.split('*')[:2])
```
Về cơ bản, đoạn comment này không cung cấp thêm thông tin nào mới. Nếu bạn đọc code, thì cuối cùng bạn cũng sẽ hiểu được nó làm gì. Nhưng với hầu hết lập trình viên, đọc comment sẽ nhanh hơn nhiều việc đọc hiểu code.

### Đừng comment chỉ vì thích comment
Có những giáo sư yêu cầu sinh viên của họ comment vào mỗi function trong bài tập về nhà. Và kết quả là có những lập trình viên cảm thấy "tội lỗi" về việc để function "trần như nhộng" không một dòng comment. Cuối cùng, họ comment bằng cách viết lại tên function và tham số của nó dưới dạng câu văn bình thường:
```java
// Find the Node in the given subtree, with the given name, using the given depth.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
```
Ví dụ trên được liệt kê vào loại "những comment vô nghĩa" khi mà việc khai báo function và comment gần như giống nhau. Đoạn comment này nên xóa đi hoặc viết chi tiết hơn.

Nếu bạn muốn comment ở đây, thì nên viết kỹ lưỡng hơn:
```java
// Find a Node with the given 'name' or return NULL.
// If depth <= 0, only 'subtree' is inspected.
// If depth == N, only 'subtree' and N levels below are inspected.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
```

### Đổi tên biến (tên hàm), thay vì comment vào những tên biến (tên hàm) tối nghĩa

Không nên comment vào tên biên (tên hàm) tối nghĩa. Ví dụ:
```java
// Enforce limits on the Reply as stated in the Request,
// such as the number of items returned, or total byte size, etc.
void CleanReply(Request request, Reply reply);
```

Phần lớn comment giải thích "clean" nghĩa là gì. Thay vào đó, nên thêm cụm từ "enfore limits" vào tên hàm:
```java
// Make sure 'reply' meets the count/byte/etc. limits from the 'request'
void EnforceLimitsFromRequest(Request request, Reply reply);
```

Tên hàm này tự "comment chính mình". Tên biến có nghĩa sẽ tốt hơn là một comment có nghĩa bởi vì nó sẽ được nhìn thấy ở mọi chỗ mà function đó được sử dụng.

Dưới đây là một ví dụ nữa về việc comment vào một tên hàm vô nghĩa:
```java
// Releases the handle for this key. This doesn't modify the actual registry.
void DeleteRegistry(RegistryKey* key);
```
`DeleteRegistry()` nghe có vẻ như là một function nguy hiểm. Đoạn comment "This doesn’t modify the actual registry" là để giải thích sự nhầm lẫn về tính nguy hiểm của function này.

Thay vào đó, chúng ta nên sử dụng tên hàm tự "comment chính mình" như sau:
```java
void ReleaseRegistryHandle(RegistryKey* key);
```

Tổng kết lại, không nên comment theo kiểu "què quặt" - là comment vào những đoạn code lởm.
Coder nên tuân theo luật "good code > bad code + good comments".

## Ghi lại những ý tưởng của bạn trong khi code
Bạn đã biết những điều không nên trong việc comment, giờ hãy thảo luận về việc nên comment điều gì. Rất nhiều comment hay được viết ra đơn giản chỉ bằng cách "ghi lại ý tưởng".

### Thêm "ghi chú của đạo diễn"

Thông thường các bộ phim hay có những đoạn "ghi chú của đạo diễn" đưa ra góc nhìn và những câu chuyện của nhà làm phim giúp bạn hiểu về cách bộ phim được làm ra. Tương tự như vậy, bạn nên thêm những góc nhìn về code vào trong comment của mình.

Dưới đây là một ví dụ:
```
// Surprisingly, a binary tree was 40% faster than a hash table for this data.
// The cost of computing a hash was more than the left/right comparisons.
```
Đoạn comment này giải thích cho người đọc một vấn đề gì đó và nó giúp họ tránh khỏi việc tốn thêm thời gian để cố gắng tối ưu hóa đoạn code.

Dưới đây là một ví dụ khác:
```
// This heuristic might miss a few words. That's OK; solving this 100% is hard.
```
Nếu không có đoạn comment này, người đọc có thể sẽ nghĩ đây là bug và sẽ cố gắng tốn thời gian test theo test case, hoặc cố gắng fix bug này.

Một comment có thể giải thích lý do vì sao code hiện tại không được clean
```
// This class is getting messy. Maybe we should create a 'ResourceNode' subclass to
// help organize things.
```

Đoạn comment này thừa nhận code đang lộn xộn và khuyến khích người code sau refactor lại. Nếu không có đoạn comment này, nhiều coder sẽ cảm thấy "bị dọa" với độ lộn xộn và ngại động vào nó.

### Comment những đoạn "code thối"

Code sẽ phát triển theo thời gian và chắc chắn sẽ không tránh khỏi những sai sót trong quá trình phát triển. Đừng ngần ngại comment những đoạn "code thối" này. Ví dụ, comment vào những chỗ mà có thể cải tiến:
```
// TODO: use a faster algorithm
```
hoặc khi code chưa hoàn thiện
```
// TODO(dustin): handle other image formats besides JPEG
```
Có rất nhiều marker phổ biến trong giới dev:
|Marker|Ý nghĩa phổ biến|
|-|-|
|TODO:|Nội dung tôi chưa làm hoặc chưa tìm hiểu|
|FIXME:|Code lởm ở đây|
|HACK:|Solution này chưa phù hợp với problem|
|XXX:|Cẩn thận không toang!|

Team của bạn có thể sẽ có những rule riêng về việc sử dụng những marker này. Ví dụ, `TODO:` dùng để thể hiện những issue đang tạm thời dừng lại. Nếu vậy, đối với những đoạn code ít lởm hơn, bạn có thể sử dụng `todo:` hoặc `maybe-later:`.

Điều quan trọng là bạn nên luôn luôn sáng tạo trong việc comment dựa trên những ý tưởng của bạn về việc code sẽ thay đổi trong tương lai. Những đoạn comment như vậy giúp người đọc có những góc nhìn chuẩn xác về chất lượng và tình trạng code và thậm chí có thể giúp họ nghĩ ra hướng cải tiến chúng.

### Comment vào hằng số
Khi định nghĩa một hằng số, sẽ có "tiểu sử" về việc hằng số đó làm gì hoặc tại sao nó lại có giá trị đó. Ví dụ:
```
NUM_THREADS = 8
```
Có vẻ như dòng này không cần comment, nhưng có khả năng thanh niên dev viết ra dòng này sẽ biết nhiều hơn:
```
NUM_THREADS = 8 # as long as it's >= 2 * num_processors, that's good enough.
```
Người đọc dòng code trên sẽ có thêm thông tin về việc chỉnh sửa hằng số trên (ví dụ: 1 thì quá ít mà 50 thì quá nhiều).

Có nhiều khi giá trị của hằng số lại không quan trọng:
```
// Impose a reasonable limit - no human can read that much anyway.
const int MAX_RSS_SUBSCRIPTIONS = 1000;
```

Nhiều khi có những giá trị đã ở mức tối ưu, và không cần chỉnh sửa nữa:
```
image_quality = 0.72; // users thought 0.72 gave the best size/quality tradeoff
```
Trong tất cả các ví dụ trên, bạn nghĩ không cần thiết phải viết comment, nhưng chúng đều khá hữu ích.

Có những hằng số không cần comment, bởi vì cái tên của chúng đã đủ rõ ràng (ví dụ: SECONDS_PER_DAY). Nhưng với trải nghiệm của chúng tôi, gần như tất cả hằng số đều có thể cải tiến bằng cách viết comment.

Hãy ghi lại suy nghĩ của bạn khi khởi tạo giá trị cho hằng số đó.

## Đặt hoàn cảnh vào vị trí của người đọc để viết chính xác những gì họ cần

Một trong những phương pháp chúng tôi sử dụng trong cuốn sách này là lường trước code của bạn sẽ ra sao đối với một người ngoài - một người không hiểu rõ về dự án của bạn. Phương pháp này đặc biệt hữu dụng để giúp bạn nhận ra nên comment điều gì.

### Dự đoán các câu hỏi

Khi một người nào đó đọc code của bạn, có những phần có thể khiến họ tự hỏi: "Hửm? Đoạn này nghĩa là sao nhỉ?" Việc của bạn là comment những phần đó.

Ví dụ, hãy nhìn vào định nghĩa của function `Clear()`:
```c++
struct Recorder {
    vector<float> data;
    ...
    void Clear() {
        vector<float>().swap(data); // Huh? Why not just data.clear()?
    }
};
```
Hầu hết dev C++ nhìn thấy đoạn code này, họ sẽ nghĩ: "Chỉ cần viết `data.clear()` là xong mà? Cần gì phải swap với một vector empty?" Hóa ra, đây là cách duy nhất để một vector thực sự xóa memory của nó trong memory allocator. Đây là một trong những chi tiết ít được biết đến trong C++. Nên comment đoạn này như sau:
```c++
// Force vector to relinquish its memory (look up "STL swap trick")
vector<float>().swap(data);
```

### Dự đoán những "hố bẫy" trong code

Khi viết function hoặc class, bạn nên tự hỏi: Đoạn code này có gì đáng chú ý không? Nó có thể bị hiểu sai như thế nào?" Về cơ bản, bạn nên nghĩ và dự đoán trước những vấn đề mà người đọc có thể gặp khi đọc code của bạn.

Ví dụ, giả sử bạn viết một function gửi email cho một user:
```java
void SendEmail(string to, string subject, string body);
```

Việc implement của function này liên quan đến việc connect đến một external service có thể tốn một vài giây hoặc lâu hơn. Một người khác có thể không nhận ra điều này và vô tình gọi function này trong khi đang xử lý một request HTTP (điều này có thể khiến web app "toang" nếu external service này bị down)

Đề ngăn chặn việc này, bạn nên comment như sau:
```java
// Calls an external service to deliver email. (Times out after 1 minute.)
void SendEmail(string to, string subject, string body);
```

Dưới đây là một ví dụ khác. Giả sử bạn có function `FixBrokenHtml()` dùng để fix những thẻ HTML bằng cách thêm closing tag hoặc những thứ tương tự như vậy:
```
def FixBrokenHtml(html): ...
```

Function hoạt động trơn tru, ngoại trừ cảnh báo rằng thời gian chạy sẽ tăng lên khi các thẻ nested và không khớp nhau. Nếu input xấu, function này có thể tốn cả phút.

Thay vì để người dùng tự "trải nghiệm", bạn nên comment như sau:
```
// Runtime is O(number_tags * average_tag_depth), so watch out for badly nested inputs.
def FixBrokenHtml(html): ...
```

### Comment mang tính "toàn cảnh"

Một trong những điều khó nhất đối với member mới trong team của bạn là "bức tranh toàn cục", cách các class tương tác với nhau, data flow như thế nào, component giao tiếp với nhau ra sao. Người design hệ thống thường quên comment về nội dung này bởi vì anh ta đã quá quen thuộc với nó.

Hãy tưởng tượng có một member mới vào team và bạn cần giúp member đó làm quen với source code.

Trong khi giải thích qua về source code, bạn có thể chỉ ra ở một số chỗ: "Đây là điểm mấu chốt giữa business logic và database. Không có chỗ nào khác gọi trực tiếp đến đây. Class này trông có vẻ phức tạp, nhưng thực chất chỉ là một bộ nhớ smart cache thôi. Nó không gây ảnh hưởng nhiều đến phần còn lại của hệ thống đâu."

Sau một vài phút, member mới sẽ biết nhiều hơn là tự đọc code.

Và đây chính là những thông tin mà bạn nên đưa vào trong comment ở cấp hệ thống.

Ví dụ:
```
// This file contains helper functions that provide a more convenient interface to 
```
hoặc 
```
// file system. It handles file permissions and other nitty-gritty details.
```

Đừng nghĩ rằng bạn phải viết một tài liệu chỉnh chu và chính thức. Một vài câu chọn lọc tốt hơn là không có gì cả.

### Comment tổng quát

Ngay cả bên trong function, bạn nên comment mang tính "tổng quát" một chút. Dưới đây là một ví dụ:
```
# Find all the items that customers purchased for themselves.
for customer_id in all_customers:
    for sale in all_sales[customer_id].sales:
        if sale.recipient == customer_id:
...
```

Nếu như không có comment, mỗi hàng sẽ là một bí ẩn (Tôi hiểu là đang lặp qua tất cả customer ... nhưng mà để làm gì?)

Đặc biệt hữu ích nếu có những comment này trong các function lớn:
```
def GenerateUserReport():
  # Acquire a lock for this user
  ...
  # Read user's info from the database
  ...
  # Write info to a file
  ...
  # Release the lock for this user
```
Những comment này cũng đóng vai trò như một bản tóm tắt có dấu đầu dòng về chức năng hoạt động, vì vậy người đọc có thể biết được ý chính của chức năng trước khi đi sâu vào chi tiết. (Nếu những phần này đọc rất dễ hiểu thì không cần thiết phải comment. Như chúng tôi đã đề cập, good code > bad code + comment)

> BẠN NÊN COMMENT VỀ Ý NGHĨA? LÝ DO? HAY CÁCH THỨC HOẠT ĐỘNG?
> 
> Có thể bạn đã nghe lời khuyên kiểu như: "Hãy comment lý do tại sao chứ không nên comment về ý nghĩa, giải thích (hoặc cách thức hoạt động)". Những lời khuyên này khá đơn giản và dễ hiểu và có những ý nghĩa khác nhau với những người khác nhau. Lời khuyên của chúng tôi là viết bất cứ thứ gì giúp người đọc hiểu code một cách dễ dàng hơn. Điều này có thể liên quan đến ý nghĩa, lý do hay cách thức hoạt động (hoặc cả ba).

## Lời kết - Vượt qua tâm lý ngại viết

Phần lớn coder không thích viết comment bởi vì cảm giác phải tốn rất nhiều công sức để viết ra một comment tốt. Khi có kiểu "ngại viết" như thế này, cách tốt nhất là cứ bắt đầu viết. Vì vậy, nếu lần tới khi bạn ngần ngại viết comment, hãy cứ viết những gì bạn nghĩ, nhưng hãy chau chuốt một chút nếu có thể.

Lưu ý là chúng tôi đã chia nhỏ công việc viết comment thành các bước đơn giản hơn sau:
1. Viết bất cứ thứ gì nảy ra trong đầu bạn
2. Đọc lại comment, và xem xem những thứ có thể cải thiện
3. Cải thiện chúng

Càng comment thường xuyên, bạn sẽ nhận ra rằng chất lượng comment ở bước 1 càng tốt hơn và cuối cùng thì không cần sửa chút nào nữa. Và bằng việc comment sớm và thường xuyên, bạn sẽ tránh được tình trạng phải viết nhiều comment một lúc.

## Tổng kết
Mục đích của một comment là giúp người đọc biết những ý tưởng của người viết code. Toàn bộ chương này là về việc nhận thức những thông tin không rõ ràng trong code và việc viết comment.

Khi nào không cần/không nên comment:
- Chỉ cần đọc code là đã đủ để hiểu.
- Nếu gặp code lởm thì nên sửa code (thay vì viết comment cho ... code lởm).

Những ý tưởng nên ghi lại:
- Lý do tại sao code như thế này chứ không phải như thế kia (ghi chú của đạo diễn).
- Sử dụng những marker như `TODO:` hoặc `XXX:`.
- Lý do vì sao một hằng số lại có giá trị như thế.

Đặt mình vào vị trí người đọc:
- Dự đoán những câu hỏi của người đọc.
- Viết lại những behavior quan trọng trong code mà người đọc bình thường không ngờ tới.
- Sử dụng comment mang tính toàn cảnh.
- Tóm tắt lại các code block với comment để người đọc không bị rối khi đọc function lớn.
