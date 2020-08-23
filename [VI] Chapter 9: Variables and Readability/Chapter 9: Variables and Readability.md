# CHAPTER NINE: Variables and Readability

![](https://i.imgur.com/RD34tXo.png)

Trong chương này, bạn sẽ thấy việc sử dụng biến cẩu thả sẽ làm cho một chương trình trở nên khó hiểu.

Đặc biệt, có ba vấn đề mà chúng ta cần phải nói đến ở đây:
    1. Càng nhiều biến hơn, cáng khó để theo dõi tất cả chúng
    2. Biến có phạm vi sử dụng càng lớn hơn, ban càng khó theo dõi hơn
    3. Biến càng dễ thay đổi, bạn cáng khó theo dõi giá trị hiện tại của nó

Ba phần tiếp theo trong chương này, chúng ta sẽ thảo luận cách giải quyết những vấn đề đã nêu ở trện.

## Loại bỏ các biến
Trong chương 8, Breaking Down Giant Expressions, chúng ta đã chỉ ra việc giới thiệu về cách sử dụng, tổng quan về các biến giúp cho việc đọc code trở nên dễ dàng hơn. Những biến đó hữu dụng vì chúng đã phá bỏ các biểu thức khổng lồ và hoạt động như một dạng tài liệu.

Trong phần này, chúng ta tập trung vào việc loại bỏ các biến không cải thiện tính dễ đọc. Khi một biên như thê này bị xóa bỏ, code mới sẽ trở nên dễ đọc hơn.

Trong phần tiếp theo là một vài ví dụ về việc những biến không cần thiết hiển thị như thế nào

### Các biến tạm thời không cần thiết
Trong đoạn code Python sau, hãy chú ý biến now:
```
now = datetime.datetime.now()
root_message.last_view_time = now
```
Theo bạn, biến now có phải một biến giá trị? Không, tất nhiến là không rồi và đây là nguyên do:

- Nó không có tác dụng trong việc phá bỏ các biểu thức tạp
- Nó không thêm sự rõ ràng cho biển thức, datetime.datetime.now() là đủ minh bạch
- Nó chỉ sử dụng một lần, do đó nó không dùng để xóa bó các đoạn code thừa
Nếu không dùng biên now, đoạn code dễ dàng hiểu như sau:
```
root_message.last_view_time = datetime.datetime.now()
```
Các biến như now thường là các biến thừa còn lại sau khi code đã được chỉnh sửa. Biến now có thể được sử dụng nhiều lần ở lúc ban đầu. Hoặc cũng có thể người code dự đoạn nó sẽ được sử dụng nhiều lần, nhưng sự thực là không bao giờ cần nó

### Loại bỏ các kết quả trung gian
![](https://i.imgur.com/di0hmzj.png)
```javascript=
var remove_one = function (array, value_to_remove) {
    var index_to_remove = null;
    for (var i = 0; i < array.length; i += 1) {
        if (array[i] === value_to_remove) {
            index_to_remove = i;
            break;
        }
    }
    if (index_to_remove !== null) {
        array.splice(index_to_remove, 1);
    }
};
```
Biến index_to_remove chỉ để chứa kết quả trung gian. Các biến như thế này thỉnh thoảng có thể loại bỏ bằng cách xử lí kết quả sớm nhất có thể khi bạn lấy được ra chúng:
```javascript=
var remove_one = function (array, value_to_remove) {
    for (var i = 0; i < array.length; i += 1) {
        if (array[i] === value_to_remove) {
            array.splice(i, 1);
            return;
        }
    }
};
```
Bằng việc code sử dụng early-return, chúng ta loại bỏ được việc sử dung biến index_to_remove cũng như việc đơn giản hóa code.

Nhìn chung, nó là một cách tiếp cận tốt để hàm được xử lí một cách nhanh nhất có thể.

### Loại bỏ các biến điều khiển luồng

Thỉnh thoảng bạn sẽ nhìn thấy kiểu code sau trong các vòng lặp:

```javascript=
boolean done = false;
    while (/* condition */ && !done) {
    ...
    if (...) {
        done = true;
        continue;
    }
}

```

Biến done có thể được được gán giá trị true nhiều lần trong vòng lặp.

Code như thế này thường cố gắng đáp ứng một số quy tắc bất thành văn mà bạn không nên thoát ra giữa vòng lăp. Không có quy tắc như vậy!

Các biến như done là cái chúng ta gọi là "biến điểu khiển luồng". Mục đích duy nhất của chúng là chỉ ra cách chương trình thực thi, chúng không giữ bất kì giá trị thực nào của chương trình. Theo kinh nghiệm của chúng tôi, biến điều khiển luồng có thể bị loại bỏ bằng việc sử dụng tốt hơn các cấu trúc chương trình:

```
while (/* condition */) {
    ...
    if (...) {
        break;
    }
}
```
Trường hợp này khá dễ để sửa, nhưng nếu có nhiều vòng lặp lồng nhau, thì việc sử dụng một câu lệnh break đã là đủ? Trong các trường hợp phức tạp như vậy, giải pháp thường liên quan đến việc đưa code tới một hàm mới(có thể code trong vòng lặp, hoặc cả vòng lặp)

> BẠN CÓ MUỐN NGƯỜI ĐỒNG NGHIỆP CẢM THẤY HỌ NHƯ TRONG MỘT BUỔI PHỎNG VẤN Ở MỌI THỜI GIAN?
> Microsoft’s Eric Brechner đã nói về việc một buổi phỏng vấn tuyệt vời nên liên quan dến ít nhất ba biên.* Đó có lẽ là việc sử lí ba biến một lúc sẽ khiến bạn phải nghĩ nhiều hơn. Điều này là hợp lí trong một buổi phỏng vân, nơi bạn đang cố gắng đẩy ứng viên tới giới hạn.
> Nhưng bạn có muốn người đồng nghiệp cảm thấy họ đang trong cuộc phỏng vấn khi họ đang đọc code của bạn?

## Giảm thiểu phạm vi của biến

Chúng ta tất đều đã nghe về lời khuyên "không sử dụng các biến toàn cục". Đây là một lời khuyên tốt, bởi vì rất khó để theo dỗi các biến toàn cục đươc sử dụng ở đâu và như thế nào. Và bằng việc vi phạm phạm vị(đặt một loạt các tên có thể xung đột với các biến cục bộ của bạn), code có thể vô tình sửa đổi một biến toàn cục khi bạn chỉ có ý định sử dụng biến cục bộ và ngược lại.

Trong thực tế, có một ý tưởng tốt về việc giảm thiểu phạm vị của tất cả các biến, không chỉ các biến toàn cục/

> Ý TƯỞNG CHÌA KHÓA
> Làm các biến của bạn dễ nhìn nhất có thể chỉ qua một vài dòng code

Nhiều ngôn ngữ lập trinh đưa ra nhiều mức độ phạm vi bao gồm module, lớp, hàm, khối phạm vi. Sử dụng các phạm vi càng hẹp nhìn chung tốt hơn, vì biến của bạn có thể "nhìn thấy" bởi vài dòng code.

Tại sao lại như vậy? Bời vì nó đơn giản giảm thiểu số lượng biến người đọc phải nghĩ trong cùng một khỏang thời gian. Nếu bạn giảm thiểu phạm vi sử dụng của tất cả các biến theo hệ số hai, thì trung bình sẽ có một nửa số biến có thể nhìn thấy trong phạm vi bất kì lúc nào.

Ví dụ bạn có một lớp lớn, với những biến thành viên sử dụng bởi chỉ hai phương thức, như sau:

```
class LargeClass {
    string str_;
    void Method1() {
        str_ = ...;
        Method2();
    }
    void Method2() {
        // Sử dụng _str
    }
    // Nhiều phương thức không sủ dụng _str ...
};

```
Ở một khía cạnh nào đó, một biến class như là một biến "toàn cục nhỏ" trong class. Cho các lớp to hơn, thì việc theo dõi tất cả các biến class trở nên khó khăn. Càng ít biến "toàn cục nhỏ", càng tốt.

Trong trường hợp này, sẽ là hợp lí sử dụng str như là một biến địa phương:

```
class LargeClass {
    void Method1() {
        string str = ...;
        Method2(str);
    }
    void Method2(string str) {
        // Sử dụng str
    }
    // Các phương thức khác không sử dụng str.
};
```
Một cách khác để giới hạn các biến class là việc tạo ra nhiều phương thức tĩnh nhiều nhất có thể. Phương thức tĩnh là cách tuyệt vời cho người đọc biết rằng "nhưng dòng code này độc lập với các biến đó".

Hoặc một cách tiếp cận khác là việc chia nhỏ các lớp to thành các lớp con. Cách tiếp cận này hữu dụng chỉ khi các lớp nhỏ hơn độc lập với nhau. Nếu bạn tạo ra hai lớp con kết nôi các biến của nhau, bạn thực sự không làm tốt hơn điều gì cả.

Tương tự việc chia nhỏ các tệp thành các tệp nhỏ hơn hoặc các chức năng lớn thành các chức năng nhỏ. Động lực lớn để làm như vậy là độc lập dữ liệu.

Các ngôn ngữ lập trình khác nhau có các quy tắc khác nhau cho việc tạo ra một phạm vi. Chúng tôi muốn chỉ ra một vài quy tắc thú vị liên quan đến phạm vi của biến.

### Câu lệnh điều kiện trong C++

Giả sử bạn có đoạn code như C++ như sau:

```
PaymentInfo* info = database.ReadPaymentInfo();
    if (info) {
        cout << "User paid: " << info->amount() << endl;
    }
// Nhiều dòng code phía sau ...
```

Thông tin biến sẽ được giữ trong phạm vi so trong phần còn lại của hàm, do đó một người đang đọc những dòng code này sẽ giữ nó ở lại trong đầu, và băn khoăn có sẽ được sử dụng lại một lần nữa.

Nhưng trong trường hợp này, biến info chỉ sử dụng ở trong câu lệnh điều kiện. Trong C++, chúng ta chỉ có thể thực sự định nghĩa thông tin trong biểu thức điều kiện:

```
if (PaymentInfo* info = database.ReadPaymentInfo()) {
    cout << "User paid: " << info->amount() << endl;
}
```

Bây giờ người đọc có thể dễ dàng quên đi biến info vì nó đã đi cùng với phạm vi.

### Tạo ra biến "riêng tư" trong Javascript

Giả sử bạn có một biến chỉ được sử dụng bởi một hàm:

```
submitted = false; // Chú ý: biến toàn cục
var submit_form = function (form_name) {
    if (submitted) {
        return; // không gửi biểu mẫu 2 lần
    }
    ...
    submitted = true;
};
```

Biến toàn cục như submmited có thể gây cho người đọc code này rất nhiều sự khó khăn. Dường như chỉ có hàm submit_form() sử dụng submmited, nhưng bạn không chắc về điều đó. Trong thực tế, một tệp Javascript khác cũng có thể sử dụng biến submmited, cho mục đích khác!

Bạn có thể phòng tránh vấn đề này bằng việc đóng gói biến subbmited trong closure:

```javascript=
var submit_form = (function () {
    var submitted = false; // Chú ý: biến chỉ có thể được nhìn thấy ở hàm dưới
    return function (form_name) {
        if (submitted) {
                return; // không gửi biểu mẫu 2 lần
            }
            ...
        submitted = true;
        };
}());

```
Chú ý dấu ngoặc đơn ở dòng cuối ngầm định rằng hàm ngoài được thực thi ngay lập tức, trả lại hàm trong.

Nếu bạn chưa nhìn thấy kĩ thuật này trước đó, nó có thể trông kì lạ lúc đầu. Nó có hiệu ứng tạo ra một phạm vi riêng tư dẫn đến chỉ các hàm trong mới có thể nhìn thấy. Bây giờ người đọc không phải băn khoăn về việc liệu biến submmited sẽ đươc sử dụng lại. (Đọc JavaScript: The Good Parts bởi Douglas Crockford [O’Reilly, 2008] cho nhiều kĩ thuật giống thế này)

### Javascript phạm vi toàn cục

Trong Javascript, nếu bạn quên từ khóa var cho việc định nghĩa biến(ví dụ x = 1 thay vì var x = 1), thì biến đó sẽ được đưa vào phạm vi toàn cục, nơi mọi tệp Javascript và các khối `<script>` có thể kết nối nó. Đây là một ví dụ:

<script>
var f = function () {
    // Nguy hiểm: 'i' không được định nghĩa với 'var'!
    for (i = 0; i < 10; i += 1) ...
};
f();
</script>
```

Đoạn code này sẽ vô tình đưa biến i vào phạm vi toàn cục, do đó các khối phía sau có thể nhìn thấy nó:
```
<script>
    alert(i); // Alerts '10'. 'i' là một biến toàn cục!
</script>
```

Nhiều lập trình viên không ý thức được quy tắc phạm vi, nên có những hành động tạo ra các lỗi kì lạ. Một trường hợp phổ biến là việc tạo ra hai hàm có có biến địa phương giống nhau, nhưng quên mất sử dụng từ khóa var. Nhiều lập trình viên sẽ suy luận rằng máy tính của anh ta bị xâm phạm hoặc RAM đã chạy tồi.

Một quy tắc tốt nhất cho Javascript là việc luôn định nghĩa biến sử dụng từ khóa var(ví dụ var x= 1). Cách thực hành này sẽ giới hạn phạm vi của biến với các hàm bên trong nơi nó được định nghĩa.

## Không lồng các phạm vi trong Python và Javascript

Ngôn ngữ như C++ và Java có phạm vi khối, nơi biến được định nghĩa bên trong if, for, try, hoặc một cấu trúc tương tự được giới hạn trong phạm vi lồng của khối đó:

```
if (...) {
    int x = 1;
}
x++; // Lỗi compile! 'x' không được định nghĩa.
```

Nhưng trong Python và Javascript, biến được định nghĩa trong một khối là có thể nhìn thấy với cả hàm. Ví dụ chú ý việc sử dụng của biến example_value trong đoạn code Python sau:

```
# Không sử dụng example_value đến điểm này.
if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break
for logger in debug.loggers:
    logger.log("Example:", example_value)
```

Quy tắc phạm vi này gây ngạc nhiên cho nhiều lập trình viên, và code như vậy rất khó để đọc. Trong các ngôn ngữ khác, sẽ là dễ dàng hơn để tìm nơi mà biến example_value được định nghĩa lần đầu.

Ví dụ trước cũng có lỗi, nếu biến example_value không được định nghĩa ở phần đầu đoạn code, đoạn thứ hai sẽ tạo ra lỗi: "NameError: ‘example_value’ is not defined". Chúng ta có thể sửa chúng, và khiến cho đoạn code trở nên dễ đọc hơn, bằng việc định nghĩa biến example_value ở "tầng cha chung gần nhất" ( trong khái niệm lồng nhau):

``` python
example_value = None

if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break
if example_value:
    for logger in debug.loggers:
        logger.log("Example:", example_value)
```

Mặc dù vậy, đây là trường hợp có thể bị loại bỏ cùng nhau, example_value chỉ giữ kết quả trung gian, và như chúng ta đã nhìn thấy trong phần "Loại bỏ các kết quả trung gian", biến như vậy có thể loại bỏ bằng việc "hoàn thành công việc sớm nhất có thể". Trong trường hợp này, điều đó có nghĩa in ra giá trị ví dụ sớm nhắt có thể khi ta tìm thấy nó.

Đoạn code mới trông như sau:

```python=
def LogExample(value):
    for logger in debug.loggers:
        logger.log("Example:", value)
    if request:
        for value in request.values:
            if value > 0:
                LogExample(value)
                break
```

### Di chuyển phần định nghĩa xuống

Trong ngôn ngữ lập trình C yêu cầu việc định nghĩa các biến ở trên đầu hàm hoặc khối. Việc yêu cầu này đã từng là rất khó khăn, vì các hàm dài vơi nhiều biến, nó buộc người đọc phải nghãi về tất cả các biến ngay lập tức, mặc dù chúng không được sử dụng tận sau này.(C99 và C++ đã loại bỏ yêu cầu này)

Trong ví dụ dưới đây, tất cả các biến được định nghĩa một cách tự nhiên ở đầu hàm:

```python
def ViewFilteredReplies(original_id):
    filtered_replies = []
    root_message = Messages.objects.get(original_id)
    all_replies = Messages.objects.select(root_id=original_id)
    root_message.view_count += 1
    root_message.last_view_time = datetime.datetime.now()
    root_message.save()

    for reply in all_replies:
        if reply.spam_votes <= MAX_SPAM_VOTES:
            filtered_replies.append(reply)
    return filtered_replies
```

Vấn đề với ví dụ code này là việc buộc người đọc phải nghĩ về ba biến một lần, và chuyển đổi giữa chúng.

Bởi vì người đọc không cần biết về tất cả chúng cho tới sau này, sẽ là dễ dàng để định nghĩa chúng ở trước lần sử dụng đầu tiên:

```
def ViewFilteredReplies(original_id):
    root_message = Messages.objects.get(original_id)
    root_message.view_count += 1
    root_message.last_view_time = datetime.datetime.now()
    root_message.save()

    all_replies = Messages.objects.select(root_id=original_id)
    filtered_replies = []
    for reply in all_replies:
        if reply.spam_votes <= MAX_SPAM_VOTES:
            filtered_replies.append(reply)
    return filtered_replies

```

Bạn có thể băn khoăn rằng liệu all_replies là một biến thật sự cần thiết, nó có thể bị loại bỏ bằng việc làm như sau:
doing:
```
for reply in Messages.objects.select(root_id=original_id):
...
```

Trong trường hợp này, all_replies là một biến giải thích tốt nên chúng tôi sẽ giữ nó.

## Ưu tiên các biến ghi một lần


![](https://i.imgur.com/DsykYv5.png)

Cho đến nay trong chương này, chúng ta đã thảo luận việc rất khó để hiểu các chương trình ó nhiều biến. Chà, còn khó hơn khi nghĩ về các biến thay đổi liên tục. Theo dõi các giá trị của chúng thêm một mức độ khó khăn.

Để giải quyết vấn đề này, chúng tôi có một gợi ý nghe có vẻ lạ lùng, ưu tiên các biến ghi một lần.

Các biến đó cố định vĩnh viến dễ dàng hơn đê nghĩ đến. chắc chắc biến cố dịnh như:
```
static const int NUM_THREADS = 10;
```

không yêu cầu nhiều suy nghĩ của người đọc. Và lí do tương tự, việc sử dụng từ khóa const trong C++(trong Java) được khuyến khích cao.

Trong thưc tế, nhiều ngôn ngữ(bao gồm Python và Java), các kiểu dữ liệu có sẵn như string là không thể thay đổi. Cũng như James Gosling(người tạo ra Java) đã nói "[Immutables] có xu hướng thường xuyên không gặp rắc rối".

Nhưng nếu bạn không thể làm cho biến của bạn chỉ ghi một lần, nó vẫn giúp biến đí thay đổi ở ít vị trí hơn.

> Ý TƯỞNG CHÌA KHÓA
> Càng nhiều nơi một biến được vận dụng, càng khó để theo dõi về giá trị hiện tại của nó

Vậy bạn làm nó như thế nào? Làm thế nào bạn thay đổi một biến thánh biến ghi một lần? Nhiều lần nó yêu cầu tái cấu trúc code một chút, cũng như bạn sẽ xem trong ví dụ tiếp theo.

# Một ví dụ cuối

Cho ví dụ cuối cùng của chương này, chúng tôi sẽ chỉ ra một ví dụ cho việc minh họa các nguyên tắc chúng ta đã thảo luận cho tới lúc này. Giả sử bạn có một trang web với nhiều ô nhập văn bản, được sắp xếp như sau:

```htmlembedded=
<input type="text" id="input1" value="Dustin">
<input type="text" id="input2" value="Trevor">
<input type="text" id="input3" value="">
<input type="text" id="input4" value="Melissa">
...

Như bạn có thể thấy, các ids bắt đầu với input1 và tăng từ đây.

Công việc của bạn là viết một hàm có tên setFirstEmptyInput() nhận một chuỗi làm đầu vào và đưa ra giá trị rỗng đầu tiên <input> lên trang web(như trông ví dụ trên, "input3"). Hàm nên trả về phần tử DOM đã được cập nhật (hoặc null nếu có giá trị đầu vào nào thỏa mãn).

Đây là một vài dòng code không áp dụng nguyên tắc trong chương này:

```javascript=
var setFirstEmptyInput = function (new_value) {
    var found = false;
    var i = 1;
    var elem = document.getElementById('input' + i);
    while (elem !== null) {
        if (elem.value === '') {
            found = true;
            break;
        }
        i++;
        elem = document.getElementById('input' + i);
    }
    if (found) elem.value = new_value;
    return elem;
};
```

Những dòng code này sẽ hoàn thành được công việc nêu trên, nhưng trông không đẹp mắt.
Có rất nhiều cách để cải thiện đoạn code này, nhưng chúng ta sẽ thảo luận nó từ góc nhìn về các biến được sử dụng:
- var found
- var i
- var elem

Cả ba biến trên đều tồn tại trong cả ham và được viết nhiều lần. Vậy hãy thử cải thiện cách dùng từng biến.

Như chúng ta đã thảo luận ở phần đầu chương này, các biến trung gian như found có thể bị loại bỏ sớm. Đây là cách cải tiến:

```
var setFirstEmptyInput = function (new_value) {
    var i = 1;
    var elem = document.getElementById('input' + i);
    while (elem !== null) {
        if (elem.value === '') {
            elem.value = new_value;
            return elem;
        }
        i++;
        elem = document.getElementById('input' + i);
    }
    return null;
};
```
Tiếp theo, chúng ta sẽ nhìn về biến elem. Nó được sử dụng nhiều lần trong vòng lặp. Qua đoạn code trên ta thấy rằng dường như nếu biến elem là giá trị ta thưc sự lặp đi lặp lại, khi chúng ta chỉ tăng giá trị lặp thông qua i. Do đó, tái cấu trúc vòng lặp thành một vòng lặp cho biến i:

```
var setFirstEmptyInput = function (new_value) {
    for (var i = 1; true; i++) {
        var elem = document.getElementById('input' + i);
        if (elem === null)
            return null; // Search Failed. No empty input found.
        if (elem.value === '') {
            elem.value = new_value;
            return elem;
        }
    }
};

```

Đặc biệt, hãy nhìn cách biến elem được sử dụng như là biến chỉ ghi một lần mà vòng đời của nó chỉ chứa trong vòng lặp. Việc sử dụng true như là điều kiện vòng lặp là không thường thấy, nhưng đổi lại chúng ta có thể thấy định nghĩa và việc thay đổi biến i trong một dòng. (Một vòng lặp truyền thống while(true) cũng có lí).

## Tổng kết
Trong chương này, chúng ta đã thảo luận về các biến trong chương trình có thể nhanh chóng dồn lại và khó theo dõi. Bạn có thể làm cho code của bạn trở nên dễ đọc bằng biệc có ít biến hơn, và làm chúng "nhẹ nhàng" nhất có thể. Cụ thể:
- Loại bỏ các biến cản trở. Đặc biệt, chúng ta đã chỉ ra một vài ví dụ về cách loại bỏ các biến giữ "kết quả trung gian" bằng việc xử lí kết quả ngay lập tức.
- Làm giảm phạm vi của từng biên xuông nhỏ nhất có thể. Di chuyển từng biến tới một nơi mà ít dòng code nhất có thể nhìn thấy nó. Vượt khỏi tầm nhìn là vượt khỏi tâm trí
- Ưu tiên các biến ghi một lần. Các biến được đặt một lần(hoặc const, final, hoặc các loại giá trị không thể thay đổi) làm code dễ dàng hơn để hiểu.
