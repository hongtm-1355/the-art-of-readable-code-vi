# CHAPTER NINE: Variables and Readability

![](https://i.imgur.com/RD34tXo.png)

Mã nguồn sẽ trở nên vô cùng khó hiểu khi mà việc sử dụng biến bừa bãi, cẩu thả - bạn sẽ nhận thấy điều đó khi mà đọc hết chương này

Đặc biệt, một vài vấn đề mà cần phải quan tâm đó là:

    1. Việc kiểm soát số lượng biến trở nên khó khăn khi mà số lượng quá nhiều
    2. Phạm vi của biến càng lớn, bạn càng phải bận tâm đến nó nhiều hơn
    3. Với các biến thường xuyên thay đổi thì việc kiểm soát giá trị của nó càng khó

3 phần dưới đây sẽ giúp bạn giải quyết các vấn đề nêu trên.

## Loại bỏ bớt các biến không cần thiết
Ở chương 8, Đơn giản hóa các công thức khổng lồ, bạn đã được giới thiệu về việc dùng các biến với vai trò "giải thích" hoặc "tổng kết" công thức, làm mã nguồn dễ đọc hơn. Các biến có tác dụng rất lớn trong việc tách nhỏ các công thức và có thể hiểu đây như là một loại tài liệu.

Trong phần này, bạn sẽ nhận biết được các biến như thế nào sẽ không cải thiện việc đọc hiểu mã nguồn. Khi mà xóa bỏ các biến này sẽ giúp mã nguồn trở nên ngắn gọn, đơn giản hơn.

Trong các phần tiếp theo, sẽ có một vài ví dụ về các biến không cần thiết.

### Sử dụng ít biến tạm thời
Xét ví dụ sau với ngôn ngữ Python, chú ý biến now nhé:
```
now = datetime.datetime.now()
root_message.last_view_time = now
```
Biến now ở đây thật sự có giá trị gì hay không? Chắc là không vì:

- Nó không chia nhỏ biểu thức phức tạp
- Nó không mang ý nghĩa giải thích, datetime.datetime.now() đủ rõ ràng rồi
- Nó được sử dụng một lần duy nhất
Không có biến now, mã nguồn đơn giản chỉ là:
```
root_message.last_view_time = datetime.datetime.now()
```
Các biến như now thường bị sót lại sau khi mã nguồn được chỉnh sửa, có thể  ban đầu chúng được sử dụng ở rất nhiều nơi. Hoặc lúc viết, lập trình viên nghĩ rằng chúng sẽ được sử dụng nhiều lần, nhưng thực tế thì lại khác, nó chẳng được dùng mấy

### Loại bỏ các biến trung gian
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
Biến index_to_remove được sử dụng để lưu kết quả trung gian. Các biến như này thường thường sẽ được loại bỏ bằng việc xử lí kết quả ngay khi mà bạn nhận được, kiểu như:
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
Bằng việc xử lí splice và return luôn, chúng ta đã loại bỏ biến index_to_remove và đơn giản hóa mã nguồn.

Tổng kết, một chiến lược tốt là hoàn thành task càng nhanh càng tốt.

### Bỏ đi các biến điều khiển

Thỉnh thoảng bạn sẽ thấy thiết kế như này:

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

Biến done được set giá trị thành true rất nhiều lần ở trong vòng lặp.

Làm như vậy chỉ để đáp ứng một quy tắc bất thành văn là không bên thoát ra giữa vòng lặp. Không có quy tắc nào như thế cả!

Biến như done được gọi là "biến điều khiển." Mục đích của nó là chỉ đạo cách chương trình thực thi - nó không chứa bất kì dữ liệu thật sự nào cả. Với quan điểm của tôi, biến điều khiển nên bị loại bỏ bằng việc thiết kế cấu trúc chương trình tối ưu hơn:

```
while (/* condition */) {
    ...
    if (...) {
        break;
    }
}
```
Trường hợp này thì xử lí đơn giản rồi, nhưng với các vòng lặp lồng nhau mà chỉ với một lệnh break thì không đủ? Với trường hợp phức tạp thế, hướng giải quyết thường liên quan đến việc tách mã nguồn ra thành các hàm mới (hoặc là đoạn mã ở trong vòng lặp, hoặc là tổng thể cả vòng lặp)

> BẠN CÓ MUỐN ĐỒNG NGHIỆP CỦA MÌNH CẢM THẤY NHƯ LÀ
> HỌ LÚC NÀO CŨNG ĐANG Ở TRONG CUỘC PHỎNG VẤN?
> Eric Brechner của Microsoft đã nói rằng các câu hỏi phỏng vấn tuyệt vời nhất nên liên quan đến 3 biến trở lên.* Nó thật sự đúng bởi vì làm việc với 3 biến cùng một lúc sẽ gây ra khó khăn nhất định! Đấy là cảm giác cho một cuộc phỏng vấn, khi mà bạn cố gắng đẩy ứng viên đến giới hạn.
> Nhưng bạn muốn đồng nghiệp cảm thấy họ đang thực hiện một cuộc phỏng vấn khi mà họ đang đọc mã nguồn bạn viết ra à?

## Rút gọn phạm vi của các biến

Bạn đã từng nghe lời khuyên "tránh các biến toàn cục." bao giờ chưa. Đó là lời khuyên tốt đấy, bởi vì việc kiểm soát các biến này được sử dụng khi nào và như thế nào là tương đối khó khăn. Và vì sự rắc rối với tên biến (có thể nó sẽ trùng với biến địa phương), một cách vô tình, bạn đã thay đổi biến toàn cục khi mà ý định là làm với biến địa phương, hoặc ngược lại.

Thật sự, đó là một ý tưởng tuyệt vời khi mà "rút gọn phạm vi" cả tất cả các biến, không chỉ riêng với biến toàn cục

> KEY IDEA
> Làm cho biến xuất hiện càng ít dòng trên mã nguồn càng tốt

Rất nhiều ngôn ngữ lập trình cung cấp nhiều phạm vi/ cấp độ truy cập, ví dụ như module, class, function, và block. Sử dụng quyền truy cập hạn chế, nhìn tổng thể sẽ tốt hơn bởi vì nó nghĩa là biến của bạn có thể được "nhìn" thấy bởi ít dòng mã nguồn hơn.

Tại sao lại thế? Bởi vì nó làm giảm số lượng các biến mà người đọc phải nghĩ đến cùng một lúc một cách hiệu quả. Nếu bạn thu hẹp phạm vi của tất cả các biến theo hệ số hai, thì trung bình sẽ có một nửa số biến trong phạm vi bất kỳ lúc nào.

Ví dụ, giả sử bạn có một class rất lớn, với một biến mà được dùng bởi chỉ 2 methods, như cách dưới đây

```
class LargeClass {
    string str_;
    void Method1() {
        str_ = ...;
        Method2();
    }
    void Method2() {
        // Uses str_
    }
    // Lots of other methods that don't use str_ ...
};

```
Biến của class như thể "biến cục bộ thu nhỏ" bên trong khu vực của class. Với các class lớn, thì việc quản lí các biến này và method nào ảnh hưởng đến biến nào là việc không dễ dàng. Biến cục bộ càng nhỏ, càng tốt.

Với thường hợp này, nên thu hẹp phạm vi của biến str_ thành biến địa phương:

```
class LargeClass {
    void Method1() {
        string str = ...;
        Method2(str);
    }
    void Method2(string str) {
        // Uses str
    }
    // Now other methods can't see str.
};
```

Cách khác để hạn chế truy cập đến biến của class là tạo ra càng nhiều static methods càng tốt. Static methods là cách tốt nhất để người đọc biến "dòng mã nguồn nào được tách biệt khỏi các biến đó."

Hoặc một cách nữa là tách class thành các class nhỏ hơn. Hướng này chỉ có tác dụng nếu các lớp nhỏ hơn không ảnh hưởng qua lại lẫn nhau. Nếu bạn tạo ra 2 class mà truy cập đến nhau, thì thôi, bạn chưa đạt được mục tiêu của mình đâu.

Tương tự với việc chia nhỏ các file lớn thành các file nhỏ hơn hoặc các function lớn thành các function nhỏ hơn. Động lực lớn để làm như vậy là cô lập dữ liệu (tức là các biến).

Nhưng với các ngôn ngữ khác nhau, quy định cho phạm vi lại là khác nhau. Dưới đây, bạn sẽ thấy một số quy định thú vị liên quan đến phạm vi của biến.

### phạm vi câu lệnh If trog C++

Giả sử bạn có đoạn mã nguồn C++ như sau:
```
PaymentInfo* info = database.ReadPaymentInfo();
    if (info) {
        cout << "User paid: " << info->amount() << endl;
    }
// Many more lines of code below ...
```
Biến info sẽ vẫn tồn tại trong phạm vi của function này, sau khi đoạn if chạy xong, vì vậy, người đọc đoạn mã nguồn này sẽ phân vân việc biến này có được sử dụng nữa hay không, và được sử dụng như thế nào.

Nhưng với trường hợp này, biến info chỉ được sủ dụng trong đoạn lệnh if thôi. Trong C++, bạn có thể định nghĩa biến này trong biểu thức điều kiện:

```
if (PaymentInfo* info = database.ReadPaymentInfo()) {
    cout << "User paid: " << info->amount() << endl;
}
```

Lúc này, sau khi hết đoạn if, người đọc sẽ ko còn băn khoăn về biến info nữa

## Tạo biến "Private" trong JavaScript

Giả sử bạn có biến cố định, được sử dụng bởi một function duy nhất:

```
submitted = false; // Note: global variable
var submit_form = function (form_name) {
    if (submitted) {
        return; // don't double-submit the form
    }
    ...
    submitted = true;
};
```

Người đọc sẽ cảm thấy hơi hoảng hốt bởi việc sử dụng biến cục bộ submitted. Dường như chỉ có function submit_form() là sử dụng biến này, nhưng bạn ko chắc chắn về điều đó. Sự thật thì có thể tồn tại file Javascript khác cùng đang sử dụng một biến cục bộ với tên submitted nhưng với mục đích khác!

Bạn có thể tránh trường hợp này bằng việc bọc submitted lại:

```javascript=
var submit_form = (function () {
    var submitted = false; // Note: can only be accessed by the function below
    return function (form_name) {
        if (submitted) {
                return; // don't double-submit the form
            }
            ...
        submitted = true;
        };
}());
```
Chú ý rằng ngoặc kép ở dòng cuối cùng giúp cho function không có tên ở bên ngoài được thực thi, và sẽ trả về kết quả của function bên trong.

Nếu bạn chưa nhìn thấy kĩ thuật này bao giờ thì sẽ trông hơi lạ lẫm một chút. Nó có hiệu quả trong việc tạo ra phạm vi "riêng tư" mà chỉ có function bên trong có thể truy cập. Lúc này, người đọc không phải bận tâm việc submitted có đưọc sử dụng ở nơi nào khác hay không, hay là nó có hoat động mâu thuẫn với các biến cục bộ cùng tên khác hay không. (xem thêm tại  JavaScript: The Good Parts by Douglas Crockford [O’Reilly, 2008] để tìm hiểu các kĩ thuật tương tự.)

### Phạm vi toàn cục trong Javascript

Trong Javascript, nếu bỏ sót từ khóa var khi định nghĩa biến (ví dụ: x = 1 thay vì var x = 1), biến sẽ được sủ dụng trong pham vi toàn cục, mọi file Javascript và khối `<script>` có thể truy cập đến nó.
Dưới đây là một ví dụ:

```
<script>
var f = function () {
    // DANGER: 'i' is not declared with 'var'!
    for (i = 0; i < 10; i += 1) ...
};
f();
</script>
```

Đoạn mã nguồn này vô tình đưa i vào trong phạm vi toàn cục, vì thế, các khối lệnh sau có thể thấy và dùng nó:
```
<script>
    alert(i); // Alerts '10'. 'i' is a global variable!
</script>
```

Rất nhiều lập trình viên không hề biết đến quy định này, và nó là một trong những nguyên nhân tạo ra bugs. 
Biểu hiện phổ biến của các bugs này là khi 2 functions cùng tạo ra biến địa phương với cùng tên, nhưng quên mất việc khai báo với var. Những function này sau đấy vô tình "nói chuyện chéo" với nhau và lập trình viên sẽ nói rằng máy tính của anh ta có vấn đề, hoặc là RAM đã hết.

Việc luyện tập tốt nhất, phổ biến nhất đối với JavaScript là luôn luôn dùng var khi định nghĩa biến (ví dụ: var x = 1). Điều này làm hạn chế phạm vi của biến luôn chỉ ở trong function định nghĩa nó.


## Phạm vi lồng nhau trong Python và JavaScript

Ngôn ngữ như C++ và Java có phạm vi khối lệnh, tức là các biến định nghĩa ở trong if, for, try, hoặc cấu trúc tương tự, bị hạn chế phạm vi ở trong đấy mà thôi:

```
if (...) {
    int x = 1;
}
x++; // Compile-error! 'x' is undefined.
```

Nhưng với Python và JavaScript, biến định nghĩa trong khối sẽ "tràn ra" ngoài cả function. Chú ý biến example_value trong ví dụ hoàn toàn hợp lệ với Python này nhé:

```
# No use of example_value up to this point.
if request:
    for value in request.values:
        if value > 0:
            example_value = value
            break
for logger in debug.loggers:
    logger.log("Example:", example_value)
```
Quy tắc này gây ngạc nhiên với khá nhiều lập trinh viên, và mã nguồn như thế này thì thật khó để đọc hiểu. Với ngôn ngữ khác, nó sẽ dễ dàng cho việc tìm chỗ định nghĩa biến example_value, bạn chỉ việc nhìn vào phía tay bên trái của function mà bạn đang đứng.

Ví dụ trên thật sự có lỗi khi mà example_value không được đặt ở phần đầu của mã nguồn, phần sau của mã nguồn sẽ tạo ra lỗi: "NameError: 'example_value' is not defined". Bạn có thể sửa nó, và làm mã nguồn dễ đọc hơn, bởi việc định nghĩa example_value ở chỗ gần nhất mà nó được sử dụng:

```python=
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
Tuy nhiên, example_value cũng có thể dễ dàng bị loại bỏ trong trưòng hợp này. Biến này chỉ lưu giá trị tạm thời, và bạn đã thấy ở mục "Loại bỏ các biến tạm thời" ở trên, biến như thế thường được bỏ đi bằng cách "hoàn thành task càng sớm càng tốt." Trong trường hợp này, tức là logger.log giá trị ngay khi nhận được.

Mã nguồn trở thành:

```python=
def LogExample(value):
    for logger in debug.loggers:
        logger.log("Example:", value)
    if request:
        for value in request.values:
            if value > 0:
                LogExample(value) # deal with 'value' immediately
                break
```

### Định nghĩa biến xuống dưới

Ngôn ngữ lập trình như C thuần yêu cầu tất cả việc định nghĩa biến ở trên cùng của hàm hoặc khối lệnh. Yêu cầu này thật không hay, bởi với những hàm dài với rất nhiều biến, nó làm ngưòi đọc băn khoăn về tất cả các biến, thậm chí nếu nó không đươc sử dụng sau đó. (C99 và C++ đã bỏ quy định này.)

Ví dụ dưới đây, tất cả các biến được định nghĩa trên cùng nhất của function mà không suy nghĩ gì:

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

Điều này làm người đọc băn khoăn về 3 biến này, và sự chuyển đổi giữa chúng.

Bởi vì người đọc không cần thiết phải biết về chúng cho đến mãi sau này, do đó, thật dễ hiểu khi chuyển việc định nghĩa chúng xuống dưới, chỗ mà nó đưọc sử dụng đầu tiên:
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

Bạn sẽ phân vân rằng all_replies có cần thiết hay không, hoặc có thể bỏ việc sủ dụng nó bằng cách:

```
for reply in Messages.objects.select(root_id=original_id):
...
```
Trong trường hợp này, biến all_replies có vai trò giải thích khá tốt, vì vậy quyết định giữ lại nó cũng không tồi.

## Ưu tiên biến khởi tạo một lần

![](https://i.imgur.com/DsykYv5.png)

Trong chương này, việc khó khăn khi đọc hiểu chương trình với nhiều biến đã được đưa ra bàn bạc. Chà, nó còn khó hơn khi nghĩ về việc các biến này thay đổi như thé nào.
Kiểm soát các biến này làm tăng độ khó cuộc chơi.

Để đối phó với vấn đề này, thì phải chăng nên dùng biến khởi tạo một lần.

Biến mà "cố định vĩnh viễn" sẽ đươc nghĩ đến đầu tiên. Chắc chắn, hằng số như:
```
static const int NUM_THREADS = 10;
```

không yêu cầu người đọc suy nghĩ nhiều. Và với lí do tương tự, sử dụng const trong C++ (và cuối cùng trong Java) được khuyến khích.

Sự thật thì, với nhiều ngôn ngữ (trong đó có Python và Java), một vài kiểu được xây dụng như string là bất biến. Và James Gosling (nhà sáng lập của Java) nói, “[Immutables] tend to more often be trouble free.” (Bất biến có xu hướng thường không gặp rắc rối.)

Nhưng thậm chí, nếu bạn không thể tạo ra biến khởi tạo một lần, thì vẫn ổn nếu biến thay đổi một vài lần.

> KEY IDEAL
> Một biến càng bị thay đổi giá trị ở nhiều nơi thì việc lí luận về giá trị hiện tại của nó càng khó.

Vậy thì làm điều này bằng cách nào? Làm sao để thay đổi một biến thành khởi tạo một lần? Chà, có thể phải tái cấu trúc mã nguồn một chút đấy, như thể bạn thấy trong ví dụ tiếp theo đây.

## Ví dụ cuối cùng

Với ví dụ cuối cùng của chương này, bạn sẽ đưọc tổng hợp với nhiều nguyên tắc mà đã đưọc nói đến ở trên.
Giả sử bạn có một trang web với một số trường input text, kiểu:

```htmlembedded=
<input type="text" id="input1" value="Dustin">
<input type="text" id="input2" value="Trevor">
<input type="text" id="input3" value="">
<input type="text" id="input4" value="Melissa">
...
```

Như bạn thấy thì thuộc tính id của các thẻ input bắt đầu với input1 và tăng dần.

Yêu cầu là viết một function tên setFirstEmptyInput(), nhận tham số đầu vào là 1 chuỗi, và xét chuỗi này thành giá trị cho thẻ input trống đầu tiên của dãy. Kết quả của function sẽ trả ra thẻ input được xét giá trị (hoặc là null nếu không có thẻ input nào trống trong dãy). Dưới đây là ví dụ mã nguồn mà không được áp dụng các quy tắc đưọc nêu ra trong chương này:

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
Đoạn mã nguồn này làm đúng yêu cầu đặt ra của bài toán, nhưng không gọn gàng. Có gì đấy chưa đúng ở đây, và chúng ta giải quyết nó như thế nào?
Có một vài cách để cải thiện đoạn mã nguồn trên, đầu tiên, hãy xem xem các biến mà nó đang sử dụng:
- var found
- var i
- var elem

Ba biến này tồn tại trong cả function và đươc dùng rất nhiều lần. Cùng cải thiện cách mà chúng được sử dụng nhé.

Như bạn đã thấy ở trước đó, biến tạm thời như found thường bị loại bỏ bằng việc trả ra kết quả sớm hơn. Vì vậy nên:

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
Tiếp, hãy xem xét elem. Nó được dùng rất nhiều lần xuyên xuốt đoạn mã nguồn này thông qua các vòng lặp, làm cho giá trị của nó khó mà kiểm soát được. Cùng tái cấu trúc lại vòng lặp while nhé:

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

Để ý việc elem trở thành biến khởi tạo một lần, và vòng đời của nó được diễn ra ở trong vòng lặp. Việc sử dụng true như một điều kiện của vòng lặp thì không thấy thường xuyên, nhưng đổi lại, bạn thấy được sự định nghĩa và thay đổi của biến i trong một dòng mã nguồn. (while(true) cũng sẽ hợp lí.)

## Tổng kết
Nội dung chương này nói về việc các biến trong một chương trình nhanh chóng bị gom lại và trở nên quá tải để kiểm soát được. Bạn có thể làm cho mã nguồn của mình dễ dàng đọc hiểu hơn bởi việc dùng it biến hơn và làm chúng "nhẹ nhàng" hơn. Đặc biệt:
- Bỏ bớt các biến không thật sự cần thiết. Bạn đã thấy một vài ví dụ của việc loại bỏ các biến chứa "giá trị tạm thời" bởi việc xử lí luôn các gía trị tạm thời nhận được.
- Rút gọn phạm vi của các biến càng nhỏ càng tốt. Chuyển các biến này đến nơi mà ít dòng mã nguồn có thể trông thấy và sử dụng.
- Ưu tiên dùng biến khai báo một lần. Biến nên được xét giá trị chỉ một lần thôi (hoặc là cùng const, final, hoặc cái gì đó khác). Điều này làm việc đọc hiểu mã nguồn dễ dàng hơn.



























