# Mang thêm thông tin vào tên biến

![chapter_2_1](images/chapter_2_1.png)

Mỗi khi đặt tên biến, tên hàm hay tên các class, một số quy tắc chung được chúng ta áp dụng. Có thể là đặt tên như một đoạn comment nhỏ. Tên biến không dài tuy nhiên bạn có thể truyền tải thông tin về biến đó bằng cách chọn một cái tên tốt.

Một số cách đặt tên chúng ta có thể thấy trong các đoạn code rất mơ hồ như __tmp__, số khác trông có vẻ hợp lý như __size__, __get__ nhưng lại không mang nhiều thông tin trong đó. Trong chương này, chúng ta sẽ tìm cách làm cho tên biến mang nhiều ý nghĩa hơn.

Chương này chúng ta sẽ đề cập đến 6 vấn đề sau:

* Chọn những từ có nghĩa cụ thể
* Tránh dùng từ nhiều nghĩa đặt tên biến
* Sử dụng tên cụ thể thay cho tên biến trừu tượng
* Đính kèm thông thin vào trong tên biến (sử dụng tiền tố và hậu tố khi đặt tên)
* Độ dài tên biến như thế nào là hợp lý
* Sử dụng chuẩn đặt tên biến để mang nhiều thông tin hơn

## Chọn những từ có nghĩa cụ thể

Một phần không thể thiếu của việc đặt tên biến mang nhiều thông tin là chọn các từ có nghĩa cụ thể và tránh dùng các từ tối nghĩa.

Để dễ hiểu hơn, ta lấy ví dụ với từ __get__ rất thông dụng trong lập trình nhưng có nghĩa không rõ ràng.

```python
def getPage(url):
    ...
```

Từ __get__ trong tên hàm không nói lên nhiều điều. Hàm náy lấy thông tin về page ở trong bộ nhớ cache, trong database hay từ Internet? Nếu lấy từ Internet thì tên hàm nên được đặt là `FetchPage()` hoặc `DownloadPage()` sẽ cụ thể hơn.

Thêm một ví dụ về class BinaryTree:

```java
class BinaryTree(){
    int size();
    ...
}
```

Đâu là điều bạn mong chờ hàm `size()` ở trên trả về? Chiều cao cây nhị phân, số node trong cây hay dung lượng bộ nhớ để lưu cây nhị phân?

Vấn đề ở đây là __size__ không mang nhiều thông tin. Một số cái tên cụ thể hơn có thể thay thế như `height()`, `numNodes()`, `memoryBytes()`.

Một ví dụ khác, ta cùng xem xét class Thread sau đây

```java
class Thread{
    void stop();
    ...
}
```

Ở đây tên hàm là __stop__ có vẻ ổn, chúng ta nên đặt tên cụ thể hơn tùy thuộc vào hàm này chạy như thế nào. Nếu nó không thể hoàn tác, ta có thể đặt là `kill()` hoặc đặt tên hàm là `pause()` nếu ta có thể `resume()` thread sau khi dừng.

### Dùng các từ hoa mỹ khi đặt tên

![chapter_2_2](images/chapter_2_2.png)

Đừng ngại sử dụng các từ đồng nghĩa hoặc hỏi bạn bè đề xuất cái tên tốt hơn. Tiếng Anh là một ngôn ngữ phong phú, có rất nhiều có thể được chọn để bạn đặt tên.

Sau đây là một số ví dụ về các từ có thể được thay thể tùy vào hoàn cảnh đặt tên biến

```text
+-----------------------------------------------------------+
| Word  |                 Alternatives                      |
+-----------------------------------------------------------+
| send  | deliver, dispatch, announce, distribute, route    |
| start | search, extract, locate, recover                  |
| make  | create, setup, build, generate, compose, add, new |
+-----------------------------------------------------------+
```

Trong PHP, ta có hàm __explode()__ để chuyển đổi chuỗi thành mảng. Đó là một cái tên hay, ta có thể tưởng tượng việc phá vỡ một cái gì đó thành các mảnh nhỏ khi đọc tên hàm. Tuy nhiên tên biến cần đặt để dễ hiểu và rõ ràng hơn là một tên biến cute.

## Tránh sử dụng các tên biến như tmp hay retval

Tên biến như __tmp__, __retval__, __foo__ là các tên biến cần tránh, nó có nghĩa như "Tôi không thể nghĩ ra một cái tên". Thay vì sử dụng những tên vô nghĩa như vậy hãy __chọn những tên mô tả về đối tượng hoặc mục đích sử dụng của biến đó__.

Dưới đây là một ví dụ sử dụng __retval__ trong Javascript

```javascript
var euclidean_norm = function (v) {
    var retval = 0.0;
    for (var i = 0; i < v.length; i += 1)
        retval += v[i] * v[i];
    return Math.sqrt(retval);
};
```

Thật dễ dàng để sử dụng __retval__ khi bạn không thể nghĩ ra cái tên nào tốt hơn cho giá trị trả về của hàm. Tuy nhiên __retval__ không mang nhiều thông tin về giá trị của nó hơn là "Tôi là giá trị trả về". Tên biến tốt hơn nên mô tả về mục đích sử dụng của nó hoặc về giá trị của nó. Trong ví dụ trên giá trị trả về là căn của tổng bình phương các giá trị trong mảng `v`, vì vậy ta có thể dùng __sum_squres__ thay cho __retval__ giúp ta biết mục đích sử dụng biến và hữu ích hơn khi bắt lỗi.

Hãy thử tưởng tượng ta vô tình mắc lỗi trong vòng lặp trên

```javascript
retval += v[i];
```

Nếu ta sử dụng __sum_squres__ thì ta có thể thấy ngay sai sót trong đoạn code trên

```javascript
sum_squres += v[i]; // Rõ ràng ta đang tính tổng bình phương(squares) mà, bug đây chứ đâu!
```

Tuy vậy trong một vài trường hợp, những cái tên chung chung lại mang ý nghĩa của nó. Chúng ta hãy xem trong hoàn cảnh nào thì nên sử dụng chúng.

### tmp

Sau đây, ta xem xét về việc hoán đổi giá trị hai biến

```java
if (right < left){
    int tmp = right;
    right = left;
    left = tmp;
}
```

Trong trường hợp trên, đặt tên biến là __tmp__ cực kỳ thuyết phục. Mục đích của biến `tmp` là để lưu trữ giá trị tạm thời trong một đoạn code ngắn. `tmp` mang ý nghĩa cụ thể với người đọc rằng biến này sẽ không xuất hiện ở những nơi khác. Nó không được truyền vào trong các hàm, không gán lại cũng như không được tái sử dụng nhiều lần.

Tuy nhiên trong trường hợp sau, __tmp__ sử dụng chỉ vì lười nghĩ tên biến:

```java
String tmp = user.name();
tmp += " " + user.phone_number();
tmp += " " + user.email();
...
template.set("user_info", tmp);
```

Mặc dù biến chỉ được dùng trong phạm vi nhỏ, đặt tên biến vì nó lưu trữ giá trị tạm thời không còn là điều quan trọng nhất. Thay vào đó ta có thể đặt tên là `user_info` sẽ phù hợp với ngữ cảnh hơn.

Trong trường hợp sau, __tmp__ là một phần trong tên biến

```java
tmp_file = tempfile.namedTemporaryFile()
...
saveData(tmp_file, ...)
```

Ta cần lưu ý rằng biến được đặt tên là `tmp_file` chứ không phải là `tmp`, bởi vì nó là một file. Giả sử trong trường hợp nó được đặt tên là `tmp`.

```java
saveData(tmp,...)
```

Nếu chỉ nhìn vào dòng code trên, rõ ràng tên biến được đặt không rõ ràng, `tmp` có thể là một file, tên file hoặc cũng có thể là dữ liệu sẽ được lưu. Vậy tên __tmp__ nên được sử dụng chỉ trong trường hợp nó được sử dụng trong phạm vi ngắn và tạm thời.

### Vòng lặp

Các biến như `i`, `j`, `iter`, `it` thường được sử dụng làm chỉ số trong các vòng lặp, mặc dù đặt tên biến như vậy khá chung chung. Ta cũng nên tránh sử dụng chúng cho các mục đích khác vì sẽ gây ra khó hiểu cho người đọc.

Trong một vài trường hợp ta có thể sử dụng cách đặt tên tốt hơn thay cho __i, j, k__. Chúng ta cùng xem xét ví dụ về sử dụng vòng lặp để tìm xem người dùng thuộc câu lạc bộ nào

```C++
for (int i = 0; i < clubs.size(); i++)
    for (int j = 0; j < clubs[i].members.size(); j++)
        for (int k = 0; k < users.size(); k++)
            if (clubs[i].members[k] == users[j])
                cout << "user[" << j << "] is in club[" << i << "]" << endl;
```

Trong câu lệnh `if`, `members[]` và `users[]` đang sử dụng sai chỉ số. Khi gặp bugs như thế này rất khó để phát hiện và sửa chữa bời vì câu lệnh trên không có lỗi gì nếu đứng riêng lẻ

```C++
if (clubs[i].members[k] == users[j])
```

Trong trường hợp này, ta nên sử dụng một cái tên cụ thể hơn. Thay vì sử dụng `i`, `j`, `k`, ta có thể lựa chọn cách đặt tên `club_i` ,`members_i`, `users_i` hay ngắn gọn hơn là `ci `, `mi` , `ui`. Cách sử dụng tên như thế này giúp lỗi dễ phát hiện hơn.

```C++
if (clubs[ci].members[ui] == users[mi]) // Bug nhé, chữ cái đầu không khớp
```

Đoạn code trên đúng khi chữ cái đầu của chỉ số khớp với chữ cái đầu của mảng

```C++
if (clubs[ci].members[mi] == users[ui])
```

### Tên biến chung chung, nên hay không?

Như chúng ta đã thấy, có nhiều trường hợp đặt tên __tmp, retval, it__ là cần thiết, tuy nhiên đặt tên biến như vậy cần những lý do chính đáng.

Một vài lần, chúng ta lười trong cách đặt tên biến. Điều này có thể hiểu được, khi không biết đặt tên như thế nào cho đúng, thật dễ dàng khi chúng ta lựa chọn tên như __foo__... Tuy nhiên nếu bạn giành ra một chút thời gian để luyện tập thói quen suy nghĩ cái tên tốt hơn, bạn sẽ nhanh chóng hơn có được cách đặt tên biến ý nghĩa cho mình.

## Sử dụng tên cụ thể thay cho tên biến trừu tượng

![image_2_3](images/chapter_2_3.png)

Khi đặt tên biến, hàm, các thành phần trong chương trình, hãy mô tả nó cụ thể hơn là dùng các từ ngữ trừu tượng. Giả sử nếu bạn có một phương thức `serverCanStart()` để kiểm tra server có thể lắng nghe trên cổng TCP/IP. Cái tên __serverCanStart__ có phần trừu tượng, khó hiểu. Ta có thể dùng một cái tên như __canListenOnPort__ sẽ cụ thể hơn và mô tả trực tiếp những gì mà hàm này sẽ thực thi.

Hai ví dụ tiếp theo sẽ minh họa rõ hơn về tư tưởng của nguyên tắc này.

### Ví dụ về DISALLOW_EVIL_CONSTRUCTORS

Trong C++, nếu ta không định nghĩa hàm khởi tạo sao chép (copy constructor) hoặc toán tử gán (assignment operator) trong class thì các hàm mặc định trong C++ sẽ được sử dụng.

> Copy constructor là hàm khởi tạo cho phép ta tạo một đối tượng từ một đối tượng khác.
> Assignment operator: Trong C++ ta có thể định nghĩa lại các phép toán +, -, *, / , =... Một số ngôn ngữ khác như Ruby cũng chp phép ta làm điều tương tự.

Mặc dù tiện dụng, tuy nhiên nó dễ dàng dẫn đến rò rỉ bộ nhớ và các nguy cơ khác bởi vì chúng được thực thi ngầm định mà bạn không thể nào lường trước được.

Giải pháp đưa ra là chúng ta có thể ngăn việc tự động sử dụng __EVIL_CONSTRUCTORS__ bằng dòng code sau.

```C++
class ClassName {
    private:
        DISALLOW_EVIL_CONSTRUCTORS(ClassName);
    public:
    ...
};
```

`DISALLOW_EVIL_CONSTRUCTORS` được định nghĩa trong C++ như sau:

```C++
#define DISALLOW_EVIL_CONSTRUCTORS(ClassName) \
    ClassName(const ClassName&); \
    void operator=(const ClassName&);
```

Nếu sử dụng như trên, hai phương thức __assignment operator__ và __copy constructor__ sẽ có access modifier là private, do đó nó không thể sử dụng từ bên ngoài.

__DISALLOW_EVIL_CONSTRUCTORS__ là cách đặt tên không được hay cho lắm. Việc sử dụng từ __evil__ là quá mạnh mẽ với một vấn đề còn đang gây tranh cãi. Quan trọng hơn, cách đặt tên trên là không rõ ràng vì chúng ta không biết được nó hàm trên đang __disallow__ cài gì. Thực ra việc sử dụng hàm trên chỉ không cho phép ta sử dụng phương thức `operator=()` và `operator=()` không thực sự là một hàm khởi tạo.

Cách đặt tên trên đã được sử dụng trong một thời gian dài và đã được thay thế bởi một cái tên rõ ràng hơn.

```C++
#define DISALLOW_COPY_AND_ASSIGN(ClassName) ...
```

### Ví dụ về --run_locally

Nếu một trong những chương trình dòng lệnh (command-line program) của chúng ta có một flag __--run-locally__. Flag này hữu ích khi chúng ta muốn xem nhiều thông tin hơn trong quá trình debug nhưng sẽ làm chương trình chạy chậm hơn và flag này chỉ nên được sử dụng khi phát triển và kiểm thử phần mềm. Trên server remote(production) thì hiệu năng mới là ưu tiên hàng đầu, vì vậy flag lúc này vô dụng.

Ơ thế có vấn đề gì với cách đặt tên này à? Có đấy!

* Một thành viên mới trong team sẽ không biết flag trên dùng để làm gì? Hoặc có biết rằng nó được dùng ở local thì cũng không hiểu tại sao lại phải làm như vậy với cách đặt tên như trên.

* Thỉnh thoảng chúng ta cũng cần xem thông tin debug trên server. Thêm flag __--run_locally__ vào nhìn rất buồn cười và gây khó hiểu (tại sao flag liên quan đến local lại chạy trên remote?)

* Một trường hợp khác là khi ta muốn kiểm tra hiệu năng của chương trình ở local mà không muốn hiện ra một đống log rối mắt nên sẽ không cần đến __--run_locally__

Trong trường hợp này ta nên dùng cái tên khác như __--extra-logging__ sẽ dễ hiểu hơn

## Đính kèm thêm thông tin vào trong tên

![chapter_2_4](images/chapter_2_4.png)

![chapter_2_5](images/chapter_2_5.png)

Như đã đề cập trước đây, tên biến nên giống như một đoạn comment nhỏ. Tên biến tuy không dài tuy nhiên mọi thông tin được thêm vào đều có thể được nhìn thấy nó mỗi khi ta đọc tên biến đó.

Vì vậy nếu có một thông tin quan trọng về tên biến mà người đọc code cần phải biến thì ta nên __đính kèm thêm các từ__ vào trong tên biến. Ví dụ như nếu ta có một tên biến lưu giá trị của một chuỗi hexa:

```java
String id = "af84ef845cd8";
```

Ta nên thay cái tên trên bằng `hex_id` để lưu ý rằng đây là chuỗi có dạng hexa

### Các đơn vị trong lập trình

Nếu biến là một đơn vị đo lường (khoảng thời gian hay bytes), sẽ hữu ích hơn nếu ta thêm đơn vị vào trong tên biến

Ta cùng xem xét ví dụ về việc đo thời gian tải của một trang web

```javascript
var start = (new Date()).getTime();
...
var elapsed = (new Date()).getTime() - start;
document.writeln("Load time was: " + elapsed + " seconds");
```

Đoạn code trên không sai tuy nhiên chúng ta sẽ không có kết quả mong muốn vì `getTime()` trả về mili giây(ms) chứ không phải giây(s)

Bằng cách thêm hậu tố `__ms` vào trong tên biến, ta sẽ làm cho đoạn code trên rõ ràng hơn

```javascript
var start_ms = (new Date()).getTime();
...
var elapsed_ms = (new Date()).getTime() - start_ms;
document.writeln("Load time was: " + elapsed_ms / 1000 + " seconds");
```

Bên cạnh thời gian trong lập trình còn có rất nhiều đơn vị khác. Dưới đây là bảng các tham số của một số hàm

```text
+----------------------------------------------------------+
|         Tham số               |     Đặt lại tên          |
+-------------------------------+--------------------------+
| Start(int delay)              | delay -> delay_secs      |
| CreateCache(int size)         | size -> size_mb          |
| ThrottleDownload(float limit) | limit -> max_kbps        |
| Rotate(float angle)           | angle -> degrees_cw      |
+----------------------------------------------------------+
```

### Thêm các thuộc tính quan trọng khác

Kỹ thuật đính kèm thông tin vào trong tên biến không giới hạn ở việc thêm các đơn vị. Bạn có thể thực hành kỹ thuật này mỗi khi có những thông tin quan trọng về biến đó

Ví dụ như, nhiều lỗ hổng bảo mật có thể khai thác đến từ việc không kiểm tra dữ liệu đầu vào của hệ thống. Trong những trường hợp như vậy, tốt hơn hết ta nên dùng những tên biến như `untrustedUrl` hoặc `unsafeMessageBody`. Sau khi tiến hành kiểm tra dữ liệu, kết quả có thể lưu trong các biến `trustedUrl` hoặc `safeMessageBody`.

Dưới đây là các trường hợp khác mà ta nên thêm tiền tố hoặc hậu tố vào trong tên biến:

```text
+-----------------------------------------------------------------------------------------------------------------------------+
|                                 Trường hợp                                 | Tên biến    |       Cách đặt tên tốt hơn       |
+----------------------------------------------------------------------------+-------------+----------------------------------+
| Mật khẩu đang ở dạng text và cần được mã hóa                               | password    | plaintext_password               |
+----------------------------------------------------------------------------+-------------+----------------------------------+
| Bình luận của người dùng cần được lọc bỏ các ký tự đặc biệt trước khi được | comment     | unescapted_comment               |
| hiển thị                                                                   |             |                                  |
+----------------------------------------------------------------------------+-------------+----------------------------------+
| Các đoạn code html cần được chuyển sang định dạng utf-8                    | html        | html_utf8                        |
+----------------------------------------------------------------------------+-------------+----------------------------------+
| Dữ liệu nhận được đã được mã hóa ở dạng "url encoded"                      | data        | data_urlenc                      |
+------------------------------------------------------------------------------------------+----------------------------------+
```

Chúng ta không nên thêm các thuộc tính như __unescapted__ hoặc __utf8__ vào tất cả các tên biến trong chương trình. Chúng chỉ hữu ích trong các trường hợp đoạn code phức tạp dễ gây ra bug, biến dễ gây ra khó hiểu cho người đọc code hoặc các trường hợp đặc biệt có thể gây ra các lỗi bảo mật. Tóm lại, nếu các thông tin đó là quan trọng, hãy thêm nó vào tên biến.

### Hungarian notaion

__Hungarian notation__ là hệ thống đặt tên được sử dụng rộng rãi bên trong Microsoft. Theo đó ta sẽ thêm kiểu(type) của biến đó dưới dạng các tiền tố. Đây là một hệ thống nghiêm ngặt tập trung vào việc mã hóa các tập thuộc tính. Dưới đây là một số ví dụ

```text
+-------------------------------------------------------------------------------------------------------+
|   Tên     |                                      Ý nghĩa                                              |
+-----------+-------------------------------------------------------------------------------------------+
| pLast     | Con trỏ (p) trỏ tới phần tử cuối cùng của một cấu trúc dữ liệu                            |
| pszBuffer | Con trỏ (p) trỏ tới ký tự rỗng hay ký tự kết thúc (z) của một string (s) buffer           |
| cch       | Số các ký tự                                                                              |
| mpcopx    | Một ánh xạ (m) từ con trỏ trỏ tới một màu (pco) đến một con trỏ trỏ đến độ dài pixel (px) |
+-------------------------------------------------------------------------------------------------------+
```

Đây là ví dụ điển hình về một hệ thống sử dụng kỹ thuật đính kèm thông tin vào tên biến.

## Độ dài tên biến như thế nào là hợp lý

![chapter_2_6](images/chapter_2_6.png)

Có một quy tắc ngầm khi ta đặt tên cho biến là tên biến không nên quá dài. Chẳng ai muốn làm việc với một tên biến như thế này:

`newNavigationControllerWrappingViewControllerForDataSourceOfClass`

Tên càng dài thì chúng ta càng khó nhớ, càng tốn không gian trên màn hình và có thể cần thêm dòng để chứa nó. Các lập trình viên có thể đưa việc đặt tên biến ngắn đi quá xa bằng cách đặt tên bằng một từ, thậm chí là một ký tự. Vậy làm sao để cân bằng việc đặt tên không quá dài nhưng tên vẫn mang đầy đủ thông tin về biến đó? Đâu mới là cách đặt tên phù hợp: `d`, `days` hay `days_since_last_update`?

Quyết định độ dài tên biến phụ thuộc vào biến đó được sử dụng như thế nào. Sau đây là một số chỉ dẫn

### Tên biến ngắn nên được sử dụng khi phạm vi sử dụng biến nhỏ

Khi chuẩn bị cho một chuyến đi ngắn, ta sẽ chuẩn bị ít hành lý hơn. Cũng như vậy, biến có phạm vi sử dụng nhỏ (small scope) không cần mang quá nhiều thông tin trong tên của nó. Vì vậy, ta có thể đặt tên biến ngắn bởi vì tất cả thông tin (kiểu của biến, giá trị khởi tạo, phương thức phá hủy) đều có thể quan sát được:

```C++
if (debug) {
    map<string,int> m;
    LookUpNamesNumbers(&m);
    Print(m);
}
```

Mặc dù `m` không mang bất cứ thông tin gì, tuy nhiên không thành vấn đề bởi vì người đọc code hoàn toàn có thể hiểu những thông tin cần thiết của biến `m` để hiểu được đoạn code trên.

Tuy nhiên, thử đặt vào hoàn cảnh `m` là một thành phần của class (thuộc tính hoặc phương thức) hay một biến toàn cục nào đó và nằm trong đoạn code sau:

```C++
LookUpNamesNumbers(&m);
Print(m);
```

Đoạn code trên chứa quá ít thông tin và không tường minh, ta không thể biết được mục đích sử dụng của `m` trong đó. Vì vậy nếu biến được sử dụng trong một phạm vi lớn, tên biến cần mang đây đủ thông tin để làm cho biến đó tường minh hơn.

### Tên biến dài không còn là vấn đề nữa

Có rất nhiều lý do để hạn chế việc đặt tên biến dài, nhưng __ngại gõ tên biến dài__ không còn là vấn đề nữa. Hầu hết các text editor, IDE hiện nay đều hỗ trợ tính năng tự động nhắc lệnh và tên biến. Ngạc nhiên thay, phần lớn các lập trình viên hiện nay đều không nhận thức về sự tồn tại của chúng. Bạn có thể thử sử dụng tính năng này như sau:

1. Gõ một vài ký tự trong tên biến

2. Nhấn các phím tắt để tự động hoàn thành từ (bảng bên dưới)

3. Nếu từ được hoàn thành không đúng, tiếp tục nhấn tổ hợp phím cho đến khi tên biến xuất hiện.

Tính năng trên hoạt động tốt trên nhiều loại file, hỗ trợ nhiều ngôn ngữ khác nhau.

```text
+------------------------------------------------------+
|     Editor    |              Command                 |
+------------------------------------------------------+
| Vi            | CTRL + P                             |
| Emacs         | meta + / (ESC + /)                   |
| Eclipse       | Alt + /                              |
| Intellij IDEA | Alt + /                              |
| TextMate      | ESC                                  |
+------------------------------------------------------+
```

### Từ viết tắt và viêt tắt

Thỉnh thoảng, chúng ta thường sử dụng các từ viết tắt để khiến cho tên biến ngắn hơn, ví dụ như class được đặt tên `BEManager` thay cho `BackendManager`. Liệu cách viết tắt có gây ra nhầm lẫn?

Theo kinh nghiệm bản thân, việc viết tắt trong một dự án thực tế là một ý tưởng tồi. Nó gây ra sự khó hiểu cho một người khi mới tham gia dự án, thậm chí là ngay cả đối với người đã viết ra đoạn code đó nếu lâu ngày mới đọc lại.

Vì vậy, hãy chắc chắn rằng một người mới tham gia dự án sẽ hiểu cái tên viết tắt đó, nếu họ hiểu, bạn có thể làm vậy nếu muốn.

Một vài trường hợp viết tắt tên biến mà hầu hết các lập trình viên đều hiểu có thể kể tới như `eval` thay cho `evaluation`, `doc` thay cho `document`, `str` thay cho `string`. Do đó, họ hoàn toàn có thể hiểu ý nghĩa của `FormatStr()` khi nhìn thấy hàm này, tuy nhiên, sẽ khó có thể hiểu được `BEManager` dùng để làm gì.

### Lược bỏ các từ không cần thiết

Một vài từ trong tên biến có thể lược bỏ mà không làm mất đi ý nghĩa của tên biến đó. Ta có thể thay thế `convertToString()` bằng cái tên ngắn hơn như `toString()` mà không làm mất đi thông tin trong tên biến. Cũng như vậy, ta có thể dùng `serveLoop()` thay cho `doServeLoop()`.

## Sử dụng chuẩn đặt tên biến để mang nhiều thông tin hơn

Cách bạn sử dụng dấu gạch dưới (_), dấu gạch ngang (-) hoặc viết hoa các chữ cái đầu cũng có thể giúp ta mang thêm thông tin vào trong tên. Dưới đây là đoạn code C++ được viết theo các quy ước chuẩn trong các dự án mã nguồn mở của Google

```C++
static const int kMaxOpenFiles = 100;
class LogReader {
    public:
        void OpenFile(string local_file);
    private:
        int offset_;
        DISALLOW_COPY_AND_ASSIGN(LogReader);
};
```

Các quy chuẩn khác nhau cho các thành phần của chương trình giúp ta làm nổi bật cú pháp và giúp ta đọc code dễ dàng hơn.

Một trong những quy chuẩn đặt tên khá phổ biến đó là sử dụng __camelCase__ cho tên class và __lower_separated__ cho tên biến. Một số cách đặt tên khác có thể khiến bạn ngạc nhiên.

Ví dụ các hằng số được đặt theo chuẩn __kConstantName__ thay cho __CONSTANT_NAME__. Cách đặt tên giúp ta phân biệt với các hằng số được định nghĩa bằng `#define` trong C/C++.

Các thuộc tính/phương thức của class cũng giống như các biến bình thường, tuy nhiên ta sẽ thêm dấu gạch dưới vào cuối, ví dụ như `offset_`. Việc này sẽ giúp ta phân biệt được ngay đâu là thuộc tính/phương thức của class, đâu là các biến bình thường khác. Giả sử bạn đang phải đọc code của một class lớn và gặp phải một dòng code

```C++
stats.clear();
```

Bạn có thể thắc mắc `stats` có là thuộc tính của class này hay không? Dòng code trên có đang thay đổi trạng thái hiện tại của class? Nếu sử dụng cách đặt tên trên, ta có thể kết luận ngay `stats` là một biến cục bộ, vì nếu là thuộc tính của class nó phải được đặt tên là `stats_`.

### Một số chuẩn đặt tên khác

Tùy thuộc vào hoàn cảnh dự án hoặc ngôn ngữ bạn sử dụng sẽ có rất nhiều chuẩn đặt tên khác nhau giúp ta đặt tên biến mang nhiều thông tin hơn.

Lấy ví dụ trong quyển __Javascript: The good parts__(Douglas Crockford, O’Reilly, 2008), tác giả đề xuất nên đặt tên hàm khởi tạo (constructor) nên đặt theo camelCase với chữ cái đầu viết hoa, các phương thức của class không viết hoa chữ cái đầu.

```javascript
var x = new DatePicker(); //DatePicker() là hàm khởi tạo
var y = pageHeight(); // pageHeight() là phương thức
```

Một ví dụ khác trong Javascript, khi ta gọi các hàm trong jQuery, sẽ hữu ích hơn nếu ta thêm tiền tố __$__ vào kết quả

```javascript
var $all_images = $("img"); // $all_images là một jQuery object
var height = 250; // height thì không
```

Khi đọc đoạn code trên ta có thể biết ngay biến nào chứa kết quả trả về của jQuery

Ví dụ cuối cùng ta sẽ nói về HTML/CSS, trong các thẻ HTML ta có thể sử dung dấu gạch dưới (_) hoặc dấu gạch ngang (-) để đặt tên cho id hoặc class của thẻ đó. Sẽ hữu ích hơn nếu ta sử dung dấu gạch dưới để phân cách cách từ trong id và dấu gạch ngang để phân cách trong class

```html
<div id="middle_column" class="main-content"> ...
```

Việc quyết định sử dụng quy tắc đặt tên nào phụ thuộc vào bạn và team của bạn tuy nhiên nếu đã sử dụng quy tắc đặt tên nào, hãy sử dụng nhất quán trong cả dự án.

## Tổng kết

Chủ đề cốt lõi trong chương này là __mang thêm thông tin vào tên biến__, điều này có nghĩa là người đọc code có thể lấy được các thông tin cần thiết khi đọc tên biến, tên hàm.

Một số lời khuyên trong chương này:

* __Chọn các từ có nghĩa cụ thể__ thay vì sử dụng `get` ta có thể dùng `fetch` hoặc `download`, tùy vào hoàn cảnh cụ thể.

* __Tránh sử dụng những cái tên chung chung__ như `tmp` hay `retval` trừ khi đó là các trường hợp đặc biệt.

* __Sử dụng tên cụ thể__ để mô tả biến đó cụ thể hơn, vì dụ sử dụng `canListenOnPort()` thay cho `serverCanStart()`.

* __Đính kèm chi tiết quan trọng__ vào tên biến, ta có thể thêm `_ms` vào biến nếu đơn vị đang là mili giây, hoặc thêm tiền tố `raw_` nếu biến đó cần được loại bỏ các ký tự đặc biệt.

* __Dùng tên biến dài nếu nó có phạm vi sử dụng lớn__, không đặt tên chỉ một, hai ký tự cho các biến được sử dụng nhiều nơi trong chương trình, tên biến ngắn chỉ thích hợp khi phạm vi biến đó nhỏ.

* __Sử dụng dấu gạch dưới, viết hoa ký tự...một cách có ý nghĩa__, ví dụ ta có thể thêm dấu gạch dưới vào tên các thuộc tính của class để phân biệt chúng với các biến cục bộ khác.