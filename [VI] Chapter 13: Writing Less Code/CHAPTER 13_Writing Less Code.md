# CHAPTER THIRTEEN: Writing Less Code

![](https://i.imgur.com/J9QClkQ.png)
Một trong những kĩ năng cần thiết mà một lập trình viên cần phải học và áp dụng thuần thục đó là việc tối giản code. Mỗi dòng code bạn viết ra đều phải có ý nghĩa, không bị dư thừa hay lặp lại. Nhờ sử dụng các thư viện, loại bỏ các feature không cần thiết, bạn đã tiết kiệm đưọc vô số thời gian, và làm code của bạn sáng sủa, ngắn gọn, tối ưu hơn.

> KEY IDEA
> The most readable code is no code at all.
>

## Đừng mất công vào những features không cần thiết
Khi bắt đầu vào một dự án mới, chắc hẳn ai cũng vô cùng hào hứng bắt tay ngay vào việc tìm hiểu yêu cầu và liệt kê các features cần có. Xu hướng của các lập trình viên là kể ra những features mà không thực sự cần thiết trong scope của dự án. Đó là các features có cũng như không, không được sử dụng hoặc làm phức tạp hóa yêu cầu của dự án.

Bên cạnh đó, việc đánh giá không đúng lượng effort cần có để implement từng feature cũng là một vấn đề. Mọi người chỉ ước lượng effort đối với việc phát triển thô (thường là coding), mà quên mất các công đoạn đi kèm như bảo trì (maintain), tài liệu hóa (documentation), ...

## Luôn đặt câu hỏi và đơn giản hóa yêu cầu
Đối với các chương trình, thì yêu cầu chung đều là nhanh, kết quả chính xác tuyệt đối và có khả năng xử lí mọi dữ liệu đầu vào, tuy nhiên, đó không phải là tất cả. Việc nẵm rõ yêu cầu và đơn giản hóa bài toán, giúp tìm ra các cách giải quyết đơn giản, làm cho các dòng code bớt cồng kềnh cũng là một mục tiêu cần hướng tới. Hãy cùng xem xét một vài ví dụ dưới đây:

### Ví dụ: Bài toán định vị cửa hàng

Giả sử rằng bạn đang được thuê để viết một ứng dụng định vị cửa hàng. Bạn nghĩ rằng yêu cầu của khách hàng sẽ kiểu như: "Với mỗi địa chỉ của bất kì user nào đó (kinh độ, vĩ độ), tìm ra cửa hàng gần nhất."

Để làm đưọc điều này với độ chính xác 100%, bạn cần phải chú ý tới các yếu tố:
    • Sẽ ra sao nếu các địa chỉ này ở 2 bên của đường chuyển ngày quốc tế
    • Nếu các địa chỉ này ở gần cực Bắc và cực Nam
    • Độ dài của mỗi dặm sẽ thay đổi theo độ cong của Trái Đất

Để mà xử lí được tất cả các trường hợp này thì chắc chắn phải tốn kha khá công sức vừa code vừa nghiên cứu, chưa kể chỗ code viết ra còn có khả năng sẽ thối

Tuy nhiên, sau khi xác nhận lại với khách hàng, bận nhận ra phạm vi của bài toán chỉ là 30 cửa hàng của tiểu bang Texas. Với khu vực nhỏ, thì dường như 3 vấn đề kể trên là không cần phải bận tâm, lúc này bạn chỉ cần chạy vòng lặp qua tất cả cửa hàng, tính toán khoảng cách và lấy ra cửa hàng với khoảng cách nhỏ nhất.

### Ví dụ: Adding a cache

Bạn có một ứng dựng Java mà thường xuyên đọc các objects từ ổ cứng. Tốc độ của ứng dụng bị giới hạn bởi việc đọc này, vì vậy, bạn muốn sử dụng cache. Một chuỗi các lần đọc điển hình sẽ trông như thế này:


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

Có thể thấy, có nhiều lần truy cập đến cùng một object, vì vậy, caching sẽ thật sự có hiệu quả.

Vậy giờ cache như thế nào?

Khi gặp vấn đề này, hẳn bạn sẽ nghĩ ngay đến việc sử dụng cache với các object được đọc gần đây nhất, và không chứa object lâu rồi mới được đọc. Chưa có thư viện nào làm được điều này cả, vì vậy, đành phải bỏ công sức để tự làm thôi. Cũng không khó khăn lắm, vì bạn chắc hẳn đã từng sử dụng các cấu trúc dữ liệu kiểu như hash table, linked list trước đấy rồi nhỉ (chắc mất khoảng 100 dòng codes để xử lí yêu cầu này)

Tuy nhiên, hãy chú ý rằng quá trình này lặp lại luôn liên tiếp, vì vậy, thay vì cache với các cấu trúc dữ liệu phức tạp như trên, bạn chỉ cần sử dụng kiểu one-item cache:

```
DiskObject lastUsed; // class member

DiskObject lookUp(String key) {
    if (lastUsed == null || !lastUsed.key().equals(key)) {
        lastUsed = loadDiskObject(key);
    }

    return lastUsed;
}
```
Điều này giúp chúng ra tiếp kiệm đến 90% effort vào việc viết code, và chưowng trình cũng tốn ít bộ nhớ hơn.

Vậy đấy, hiệu quả của việc đơn giản hóa yêu cầu và tìm cách xử  lý đơn giản là không thể bỏ qua được.
Các yêu cầu thường liên quan, móc nối với nhau theo cách nào đấy. Thế nên đơn giản hóa một nửa vấn đề  thì bạn chỉ cần tốn 1/4 effort để  viết code thôi.

## Giữ cho lượng codebase càng ít càng tốt

![](https://i.imgur.com/9IWbxOa.png)

Khi bắt đầu một dự án phần mềm, bạn chỉ có một hoặc hai file trong mã nguồn, một thứ đều ổn. Biên dịch và chạy mã nguồn là chuyện đơn giản. Nó cũng dễ dàng cho việc thay đổi và ghi nhớ xem function hoặc class đó đưọc định nghĩa ở đâu.

Rồi dần dần, cùng với dự lớn lên của dự án, các thư mục cũng được nở ra và lấp đầy bởi các file mã nguồn. Số lượng các thư mục cũng tăng lên âm ỉ theo thời gian. Việc ghi nhớ các function đưọc gọi ở đâu và theo dõi xem bug sinh ra ở chỗ nào mất khá nhiều thời gian và công sức.

Cuối cùng, bạn có một lượng cực lớn mã nguồn chia ra thành các thư mục khác nhau. Dự án lớn và không một ai có thể hiểu hết. Khi đó, thêm một feature mới trở thành nỗi trăn trở và xử lí chỗ mã nguồn đó cũng trở nên cồng kềng và không vui vẻ là bao.

Cái mà tôi vừa kể với bạn ở trên là một quy luật tự nhiên mà rất nhiều hệ thống đã trải qua, sự phức tạp dường như luôn đi đôi với sự phát triển nhanh.

Cách tốt nhất để đối phó với điều này là cố gắng giữ cho codebase của bạn đơn giản, nhẹ nhàng kể cả khi dự án của bạn đang nở to ra. Để  làm đưọc thì cần:

- Xây dựng code có tính tái sử dụng, tránh những đoạn code bị lặp. (Xem tại chương 10, Extracting Unrelated Subproblems)
- Xóa những đoạn code thừa hoặc những feature vô ích. (Được nhắc đến trong chương này)
- Chia dự án hiện tại thành các dự án nhỏ hơn, ít bị phụ thuộc vào nhau.
- Có trách nhiệm với độ đơn giản của codebase.

> XÓA BỎ CODE THỪA
> Người làm vườn thường xuyên tỉa cây để giữ cho chúng phát triển. Một cách tương tự, đó là một ý tưởng tuyệt vời khi mà xòa bỏ các đoạn code không sử dụng đến.

> Mỗi khi dòng code được viết ra, lập trình viên thường lưỡng lự trong việc xóa nó, bởi vì nghĩ nó có tác dụng nào đó. Xóa code tức là tự nhận mình đã tiêu tốn thời gian vô ích trong việc viết ra nó. Thôi nào, bỏ cái tư tưởng ấy đi. Các nhiếp ảnh gia, nhà văn, nhà làm phim sáng tạo là những người không bao giờ đắm chìm mãi trong các sản phẩm của họ.
> Việc xóa bỏ các function cô lập là chuyện dễ dàng, nhưng cách để nhận biết đoạn code nào là không đưọc sử dụng trong suốt dự án thì có vẻ chưa nhiều ngưòi rõ. Hãy tham khảo qua một vài ví dụ dưới đây:
> - Ban đầu, bạn thiết kế để hệ thống của mình xử lí được cả filenames mang tính quốc tế, kiểu như filenames chứa các kí tự của bảng mã của nhiều quốc gia. Bạn đã viết code với các bộ chuyển đổi. Tuy nhiên, hiện tại hệ thống của bạn lại ko làm việc với kiểu filenames này nữa. Vậy tại sao lại ko loại bỏ nó đi nhỉ?
> - Bạn mong muốn chương trình của mình làm việc được cả khi hệ thống bị tràn bộ nhớ, do đó bạn viết một đoạn mã nguồn để cố gắng khôi phục chương trình trong tình huống này. Ý tưởng tuyệt đấy chứ. Nhưng trên thực tế, khi hệ thống bị tràn bộ nhớ, chương trình của bạn sẽ trở nên không ổn định, tất cả các chức năng chính đều không thể sử dụng được, lúc này người dùng chỉ mong muốn một cứ nhấn chuột để thoát khỏi mớ hỗn độn.
> Tại sao bạn không để chương trình của mình dừng lại chỉ với một câu thông báo đơn giản: "Đã xảy ra lỗi, xin lỗi". Đoạn code mà bạn cố gắng viết để khôi phục chương trình lúc nãy, trở nên thừa thãi.


## Mở rộng hiểu biết với các bộ thư viện

Mất rất nhiều thời gian chỉ vì các lập trình viên không nhận ra rằng các thư viện có sẵn có thể giải quyết vấn đề của họ. Hoặc đôi khi, họ quên mất rằng thư viện nào có thể làm gì. Để có thể sử dụng đưọc thư viện thì một điều quan trọng là bạn cần biết tác dụng của chúng đễn đâu.

Đây là một lời khuyên máu mủ nhắn đến bạn: thỉnh thoảng, dùng 15 phút để đọc tên của các functions, modules trong các bộ thư viện chuẩn. Đó có thể là C++ Standard Template Library, Java API, cách xây dựng modules của Python, ...

Mục tiêu của việc này không phải là ghi nhớ cả bộ thư viện khổng lồ. Nó chỉ đơn giản là tạo cảm giác quen thuộc với những thứ có sắn, rồi một lúc nào đấy, khi mà bạn đang viết code, bạn chợt nhớ ra rằng: "Chờ đã, hình như cái này giống giống với cái mình đã nhìn thấy ở ...." Tôi tin rằng việc bạn thường xuyên đọc mã nguồn của các thư viện sẽ có hiệu quả nhanh chóng, bạn sẽ có xu hướng sử dụng các thư viện này ngay khi đối mặt với vấn đề.

### Ví dụ: Lists và Sets trong Python
Giả sử, với Python, bạn muốn lấy ra các phần tử duy nhất của một list (ví dụ: [2,1,2] thì sẽ có kết quả là [2,1]). Bạn có thể hoàn thành việc này bằng cách sử dụng dictionary, bởi vì dictionary có key là duy nhất:

```python
def unique(elements):
    temp = {}
    for element in elements:
        temp[element] = None # Giá trị này không quan trọng đâu nhé
    return temp.keys()

unique_elements = unique([2,1,2])
```
Nhưng thay vì dài dòng như thế này, thì hãy dùng Set, cái mà ít người biết hơn:
```
unique_elements = set([2,1,2]) # Xóa bỏ phần tử trùng
```
Đây là một object, có thể lặp, và chỉ như một list thông thường. Nếu muốn trả ra một list object, thì chỉ cần:
```
unique_elements = list(set([2,1,2])) # Xóa bỏ phần tử trùng
```
Rõ ràng, set là sự lựa chọn đúng đắn. Nếu giả sử không biết đến set thì bạn phải tự viết ra đoạn code unique() như trên.

### Sử dụng thư viện là môt lợi thế lớn
Một thống kê chỉ ra rằng trung bình một ngày, một kĩ sư phần mềm tạo ra mười dòng code có khả năng chuyển đổi. Khi mà mới nghe lần đầu, chắc hẳn các lập trình viên đếu thốt lên răng: "Mười dòng code, tôi có thể làm điều này chỉ trong một phút!"

Tuy nhiên, mấu chốt vấn đề là ở khả năng chuyển đổi. Mỗi dòng code trong bộ thư viện là kết tinh của cả quá trình thiết kế, gỡ lỗi, ngắn gọn hóa, tài liệu hóa, tối ưu hóa và kiểm thử. Dòng code nào cũng vô cùng có giá trị. Vì thế, sử dụng thư viện là một lợi thế, nó giúp tiết kiệm thời gian và giảm bớt lượng code cần phải viết ra.

## Ví dụ: Sử dụng công cụ trong Unix thay vì viết code

Với một web server thường xuyên trả ra HTTP codes là 4xx hoặc 5xx, đó là dấu hiệu của lỗi ngầm (4xx là lỗi phía client, 5xx là lỗi phía server). Bạn muốn viết một chương trình để phân tích access logs của web server, từ đó quyết định xem những URLs nào tạo ra lỗi nhiều nhất.

Access logs thường trông giống như thế này:
```
1.2.3.4 example.com [24/Aug/2010:01:08:34] "GET /index.html HTTP/1.1" 200 ...
2.3.4.5 example.com [24/Aug/2010:01:14:27] "GET /help?topic=8 HTTP/1.1" 500 ...
3.4.5.6 example.com [24/Aug/2010:01:15:54] "GET /favicon.ico HTTP/1.1" 404 ...
...
```
Một cách tổng quát, định dạng của các dòng sẽ là:
```
browser-IP host [date] "GET /url-path HTTP/1.1" HTTP-response-code ...
```
Với C++ hoặc Java, viết một chương trình để tìm ra những url nào trả ra 4xx hoặc 5xx code nhiều nhất thì phải tốn tối thiểu 20 dòng code.

Thay vào đó, với Unix, chỉ cần command line đơn giản thế này:
```
cat access.log | awk '{ print $5 " " $7 }' | egrep "[45]..$" \
| sort | uniq -c | sort -nr
```
kết quả trả ra là:

```
95 /favicon.ico 404
13 /help?topic=8 500
11 /login 403
...
<count> <path> <http response code>
```

Tuyệt vời mà nhỉ. Command line này giúp chúng ta tránh phải viết bất kì dòng code nào.

![](https://i.imgur.com/Tit47ju.png)


## Tổng kết

> Phiêu lưu, phấn khích - một Jedi khao khát những điều này.
> —Yoda

Nội dung chương này tập trung vào việc làm sao để viết ít code nhất có thể. Mỗi dòng code mới đều cần phải kiểm thử, tài liệu hóa và bảo trì. Xa hơn nữa, càng nhiều codebase thì gánh nặng cho việc phát triển càng nhiều và nặng nề.

Bạn có thể tránh viết nhiều code bằng các cách:

- Loại bỏ các tính năng không quan trọng khỏi sản phẩm của bạn
- Xem xét lại các yêu cầu để tìm ra cách giải quyết đơn giản nhất
- Làm quen với các thư viện chuẩn bằng cách định kì đọc lại toàn bộ APIs
