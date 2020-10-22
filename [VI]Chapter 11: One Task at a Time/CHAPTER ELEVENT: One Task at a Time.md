# CHAPTER 11: Một công việc tại một thời điểm

![](https://i.imgur.com/nmnce4t.png)


Mã nguồn mà xử lí nhiều đầu việc trong một lần thì rất khó hiểu. Một block mã nguồn có thể khởi tạo object mới, làm sạch data, parse input, và xử lí logic. Nếu gộp tất cả lại với nhau thì sẽ gây khó khăn trong việc đọc hiểu hơn là mỗi đoạn mã nguồn bắt đầu và tự hoàn thành công việc nhỏ của mình.

> KEY IDEA
> Code should be organized so that it’s doing only one task at a time.
>

Nói theo cách khác, nội dung của chương này là về việc phân mảnh mã nguồn. Biểu đồ dưới đây minh họa quá trình này: bên trái mô phỏng việc mã nguồn phải làm nhiều việc cùng một lúc, và bên phải là cùng mã nguồn đấy, nhưng được tái cấu trúc để mỗi đoạn mã nguồn chỉ làm một việc tại một thời điểm thôi.

![](https://i.imgur.com/a6YkYf8.png)
Có lẽ bạn đã từng nghe nói rằng "functions chỉ nên làm một việc thôi." Lời khuyên của chúng tôi cũng tương tự, nhưng nó không chỉ nói về phạm vi của từng function đâu. Tách function lớn thành các phần nhỏ hơn là tốt. Nhưng nếu bạn không làm vậy, bạn có thể tổ chức lại code của mình ở trong function lớn, chia thành các đoạn logic riêng biệt.

Dưới đây là quá trình mà bạn thường làm để mã nguồn có thể "làm một công việc tại một thời điểm":
    1. Liệt kê tất cả các "việc" (task) mà mã nguồn cần làm. Danh từ "việc" được sử dụng nhưng với ý nghĩa nhẹ nhàng, có khi chỉ là "chắc chắn rằng object này là hợp lệ" hoặc là "lặp qua tất cả các node của tree."
    2. Cố gắng chia nhỏ các việc này các functions khác nhau hoặc ít nhất là thành các khối riêng biệt.

Trong chương này, bạn sẽ được tiếp cận một vài ví dụ về việc thực hiện quá trình trên.


## Các đầu việc nên nhỏ thôi

Giả sử rằng blog có chức năng vote, user có thể vote một comment "up" hoặc "down". Mỗi vote "up" là +1 điểm, "down" là -1 điểm. Tổng điểm của comment là tổng của vote.

Dưới đây là 3 trạng thái mà một vote của user có thể có, và ảnh hưởng của nó đến tổng điểm:

![](https://i.imgur.com/VmQycYa.png)

Khi user clicks các buttons để tạo/ thay đổi vote của họ, đoạn JavaScript sau được gọi:

```
vote_changed(old_vote, new_vote); // each vote is "Up", "Down", or ""
```
Function này cập nhật tổng điểm và thực thi cho tất cả các tổ hợp vote cũ (old_vote) và vote mới (new_vote) là up hay down:
```javascript=
var vote_changed = function (old_vote, new_vote) {
    var score = get_score();

    if (new_vote !== old_vote) {
        if (new_vote === 'Up') {
            score += (old_vote === 'Down' ? 2 : 1);
        } else if (new_vote === 'Down') {
            score -= (old_vote === 'Up' ? 2 : 1);
        } else if (new_vote === '') {
            score += (old_vote === 'Up' ? -1 : 1);
        }
    }
    set_score(score);
};
```
Mặc dù đoạn mã nguồn này khá ngắn, nhưng công việc mà nó thực thi thì tương đối nhiều. Có một sự phức tạp ở đây, và nếu chỉ nhìn lướt qua thì khó mà có thể nhận định rằng có bất kì bugs hay lỗi gõ phím nào đó hay không.

Đoạn mã nguồn dường như chỉ làm một việc duy nhất (là cập nhật số điểm), nhưng thật sự thì có 2 công việc đang được thực hiện ở đây:
    1. old_vote và new_vote đưọc chuyển đổi thành giá trị số.
    2. cập nhật tổng điểm.

Bạn có thể đơn giản hóa đoạn mã nguồn trên một cách dễ dàng bằng việc xử lí riêng rẽ từng công việc. Việc đầu tiên là chuyển vote thành số:


```javascript=
var vote_value = function (vote) {
    if (vote === 'Up') {
        return +1;
    }

    if (vote === 'Down') {
        return -1;
    }
    return 0;
};
```
Và tiếp theo là cập nhật điểm:
```javascript=
var vote_changed = function (old_vote, new_vote) {
    var score = get_score();
    score -= vote_value(old_vote); // remove the old vote
    score += vote_value(new_vote); // add the new vote
    set_score(score);
};
```
Như bạn có thể thấy, cách viết mã nguồn như thế này sẽ tốn ít công sức để suy nghĩ và cam đoan xem nó có hoạt động hay không. Đó là một nỗ lực lớn của việc biến mã nguồn trở nên dễ hiểu hơn.

## Lấy giá trị từ đối tượng

Bạn đã từng có nghĩ về việc có đoạn mã JavaScript mà chuyển đổi địa chỉ của user thành một chuỗi gồm tên nước, tên thành phố để dễ đọc và thân thiện hơn, kiểu như "Santa Monica, USA" hay là "Paris, Pháp". Chúng ta đươc cung cấp một từ điển địa chỉ (location_info) với rất nhiều thông tin. Điều mà bạn phải làm là chọn một thành phố hoặc một nước từ đó và ghép chúng lại với nhau.

Hình minh họa dưới đây là ví dụ của dữ liệu đầu vào và đầu ra:

![](https://i.imgur.com/2TQ2i7Q.png)

Có vẻ dễ dàng nhỉ, nhưng phần khó ở đây là có thể một trong 4 giá trị này sẽ bị thiếu. Cách để đối phó với trưòng hợp này là:

- Khi có thông tin về thành phố (city) thì tên địa phương (LocalityName) của thành phố đấy sẽ đưọc sử dụng. Nếu không có thì sẽ là tên khu vực hành chính (SubAdministrativeAreaName), hoặc là tên hành chính (AdministrativeAreaName)
- Nếu tất cả các giá trị trên đều không tồn tại, thì sẽ sử dụng giá trị mặc định là "Middle-ofNowhere"
- Nếu như tên nước (CountryName) không có, "Planet Earth" sẽ đưọc dùng.

Hình dưới đây minh họa 2 ví dụ cho việc xử lí khi giá trị bị thiếu.

![](https://i.imgur.com/cxoyuUm.png)

Đoạn mã nguồn xử lí cho công việc này:

```javascript=
var place = location_info["LocalityName"]; // e.g. "Santa Monica"
if (!place) {
    place = location_info["SubAdministrativeAreaName"]; // e.g. "Los Angeles"
}
if (!place) {
    place = location_info["AdministrativeAreaName"]; // e.g. "California"
}
if (!place) {
    place = "Middle-of-Nowhere";
}
if (location_info["CountryName"]) {
    place += ", " + location_info["CountryName"]; // e.g. "USA"
} else {
    place += ", Planet Earth";
}

return place;
```

Có vẻ rắc rối nhưng nó đang chạy đúng đấy.

Một vài ngày sau, bạn cần cải thiện function này: với địa chỉ ở United States, cần hiển thị tên bang thay vì tên nước (nếu có thể). Vì vậy, thay vì "Santa Monica, USA" sẽ phải là "Santa Monica, California".

Để đoạn mã nguồn trên xử lí thêm việc này thì đã rối sẽ càng rối hơn.

### Áp dụng quy tắc "Một công việc tại mỗi thời điểm"

Thay vì cố gắng kéo căng đoạn mã nguồn ra, bạn dừng lại và nhận ra rằng chỗ mã nguồn này đang có quá nhiều thứ để xử lí song song:

1. Lấy các giá trị từ từ điển địa chỉ
2. Kiểm tra sự xuất hiện của thành phố, nếu không có thì lấy giá trị mặc định là "Middle-of-Nowhere"
3. Lấy tên nước, và dùng "Planet Earth" nếu không tìm được
4. Cập nhật địa chỉ

Do đó, hãy viết lại đoạn mã nguồn nguyên bản ban đầu bằng cách chia nó ra và xử lí các công việc độc lập

Đầu tiên, lấy giá trị từ từ điển vị trí:

```
var town = location_info["LocalityName"]; // e.g. "Santa Monica"
var city = location_info["SubAdministrativeAreaName"]; // e.g. "Los Angeles"
var state = location_info["AdministrativeAreaName"]; // e.g. "CA"
var country = location_info["CountryName"]; // e.g. "USA
```
Tại đây, bạn đã hoàn thành công việc bằng cách dùng location_info và không cần phải tiếp tục nhớ các giá trị keys vừa dài vừa không trực quan này nữa. Thay vào đó là 4 biến đơn giản hơn.

Tiếp đến, bạn phải tưởng tưọng ra phần cuối của địa chỉ sẽ như thế nào:

```javascript=
// Start with the default, and keep overwriting with the most specific value.
var second_half = "Planet Earth";
if (country) {
    second_half = country;
}
if (state && country === "USA") {
    second_half = state;
}
```
Đơn giản nhỉ, và đây là phần đầu:
```javascript=
var first_half = "Middle-of-Nowhere";
if (state && country !== "USA") {
    first_half = state;
}
if (city) {
    first_half = city;
}
if (town) {
    first_half = town;
}
```
Cuối cùng, địa chỉ hoàn thiện sẽ là:
```javascript
return first_half + ", " + second_half;
```

Hình minh họa "phân mảnh hóa" ở đầu chương thật sự đã mô tả một cách trực quan ví dụ này, so sánh giữa cách giải quyết cũ và mới. Tiếp theo là hình với nhiều chi tiết đưọc thêm vào:

![](https://i.imgur.com/1wnoHYM.png)

Như bạn có thể thấy, 4 công việc ở hướng giải quyết thứ hai đã đưọc viết gọn vào thành các phần riêng biệt.

### Cách tiếp cận khác

Có nhiều cách để tái cấu trúc đưọc mã nguồn, và việc tách nhỏ công việc ở đây cũng thế. Một khi bạn tách, thì cách tổ chức mã nguồn sẽ đơn giản hơn.

Xét lại ví dụ trưóc đó, loạt câu lệnh if yêu cầu sự cẩn thận để đọc hiểu xem cách thực thi như vậy có đúng hay không. Thật ra có hai việc nhỏ đưọc xử lí ở đấy:

    1. Duyệt qua danh sách các biến, và chọn cái có sẵn ưu tiên nhất .
    2. Sử dụng một danh sách khác để lấy tên bang, tùy thuộc vào nước đó có phải là "USA" hay không.

Xem lại nhé, bạn có thể thấy rằng đoạn mã nguồn trước đấy đã có "if USA" đan xen với các logic khác. Thay vào đó, hãy xử lí riêng trường hợp là USA và không là USA một cách độc lập:
```javascript=
var first_half, second_half;

if (country === "USA") {
    first_half = town || city || "Middle-of-Nowhere";
    second_half = state || "USA";
} else {
    first_half = town || city || state || "Middle-of-Nowhere";
    second_half = country || "Planet Earth";
}

return first_half + ", " + second_half;
```
Nếu như bạn không quen với cách viết của JavaScript thì a || b || c là biểu thức và xác thực giá trị đầu tiên có đúng hay không (trong trường hợp này là có phải chuỗi trống hay không). Đoạn mã nguôn này có lợi ích là nó dễ dàng thể hiện độ ưu tiên của các biến. Hầu hết các câu lệnh if đều được loại bỏ và logic thì đưọc biểu diễn bởi ít dòng hơn.

## Ví dụ lớn

Trong một hệ thống thu nhập thông tin, môt hàm tên là UpdateCounts() đưọc gọi để tăng giá trị các thống kê khác nhau sau khi một trang web đưọc tải xuống.

```
void UpdateCounts(HttpDownload hd) {
    counts["Exit State" ][hd.exit_state()]++; // e.g. "SUCCESS" or "FAILURE"
    counts["Http Response"][hd.http_response()]++; // e.g. "404 NOT FOUND"
    counts["Content-Type" ][hd.content_type()]++; // e.g. "text/html"
}
```
Đó là mã nguồn mà bạn mong muốn!

Sự thực thì HttpDownload object không có bất kì phương thức nào như thế kia cả. Thay vào đó, HttpDownload là một class cực kì lớn và phức tạp, với rất nhiều class con, và bạn muốn nó tự trả ra các giá trị như thế kia. Trong trường hợp tồi tệ hơn, thỉnh thoảng các giá trị ấy còn bị thiếu, khi ấy, bạn sẽ dùng "unknow" như là mặc định.

Bởi vì vậy, đoạn mã nguồn thực sự trông sẽ gớm như thế này:

```
// WARNING: DO NOT STARE DIRECTLY AT THIS CODE FOR EXTENDED PERIODS OF TIME.
void UpdateCounts(HttpDownload hd) {
    // Figure out the Exit State, if available.
    if (!hd.has_event_log() || !hd.event_log().has_exit_state()) {
        counts["Exit State"]["unknown"]++;
    } else {
        string state_str = ExitStateTypeName(hd.event_log().exit_state());
        counts["Exit State"][state_str]++;
    }

    // If there are no HTTP headers at all, use "unknown" for the remaining elements.
    if (!hd.has_http_headers()) {
        counts["Http Response"]["unknown"]++;
        counts["Content-Type"]["unknown"]++;
        return;
    }

    HttpHeaders headers = hd.http_headers();

    // Log the HTTP response, if known, otherwise log "unknown"
    if (!headers.has_response_code()) {
    c   ounts["Http Response"]["unknown"]++;
    } else {
        string code = StringPrintf("%d", headers.response_code());
        counts["Http Response"][code]++;
    }

    // Log the Content-Type if known, otherwise log "unknown"
    if (!headers.has_content_type()) {
        counts["Content-Type"]["unknown"]++;
    } else {
        string content_type = ContentTypeMime(headers.content_type());
        counts["Content-Type"][content_type]++;
    }
}
```

Có thể thấy, cả tá dòng mã nguồn, và một đống logic, thậm chí còn có một số dòng bị lặp lại. Việc đọc hiểu thật là chẳng dễ chịu gì.

Đặc biệt, đoạn mã nguồn này chuyển đổi qua lại giữa các công việc khác nhau. Dưới đây là một vài chỗ xen kẽ:

1. Dùng "unknow" như là giá trị mặc định cho mỗi key
2. Kiểm tra xem các phần của HttpDownload có bị thiếu hay không
3. Lấy giá trị và chuyển nó thành kiểu chuỗi
4. Cập nhật counts[]

Hãy cải thiện nó bằng cách tách ra thành cách phần khác nhau trong mã nguồn"

```
void UpdateCounts(HttpDownload hd) {
    // Task: define default values for each of the values we want to extract
    string exit_state = "unknown";
    string http_response = "unknown";
    string content_type = "unknown";

    // Task: try to extract each value from HttpDownload, one by one
    if (hd.has_event_log() && hd.event_log().has_exit_state()) {
        exit_state = ExitStateTypeName(hd.event_log().exit_state());
    }
    if (hd.has_http_headers() && hd.http_headers().has_response_code()) {
        http_response = StringPrintf("%d", hd.http_headers().response_code());
    }
    if (hd.has_http_headers() && hd.http_headers().has_content_type()) {
        content_type = ContentTypeMime(hd.http_headers().content_type());
    }

    // Task: update counts[]
    counts["Exit State"][exit_state]++;
    counts["Http Response"][http_response]++;
    counts["Content-Type"][content_type]++;
}
```
Như bạn thấy, mã nguồn có 3 phần riêng rẽ với các muc đích:
1. Định nghĩa giá trị mặc định cho 3 keys mà chúng ta quan tâm.
2. Xác định giá trị, nếu nó tồn tại, cho mỗi keys, và chuyển chúng thành chuỗi.
3. Cập nhật counts[] cho mỗi key/value.


Điểm cộng ở đây là các phần độc lập và không dính dáng đến nhau. Khi mà đọc phàn này, bạn không cần quan tâm phần còn lại như thế nào.

Chú ý rằng mặc dù liệt kê 4 đầu việc, nhưng chỉ có 3 cái đưọc tách biệt ở đây. Điều đó hoàn toàn ổn vì các đầu việc mà đưọc liệt kê ban đầu chỉ là điểm khởi đầu. Ngay cả việc tách nhỏ một trong số chúng cũng sẽ có ích đối với tổng thể chung, như đã làm ở đây.

### Cải thiện tốt hơn

Phiên bản mới này của mã nguồn đưọc đánh giá là có cải thiện so với bản ban đầu. Và chú ý rằng bạn thậm chí chưa tạo ra functions khác để trông rõ ràng hơn. Như đã nói ở trên, ý tưởng của "một công việc tại một thời điểm" có thể giúp bạn làm mã nguồn sáng sủa hơn mặc dù chỉ trong phạm vi của function đó thôi.

Tuy nhiên, có một cách khác hay ho hơn, đó là tạo ra 3 helper functions:

```
void UpdateCounts(HttpDownload hd) {
    counts["Exit State"][ExitState(hd)]++;
    counts["Http Response"][HttpResponse(hd)]++;
    counts["Content-Type"][ContentType(hd)]++;
}
```
Những functions này sẽ trả về chính xác giá trị, hoặc là "unknown". Ví dụ:
```
string ExitState(HttpDownload hd) {
    if (hd.has_event_log() && hd.event_log().has_exit_state()) {
        return ExitStateTypeName(hd.event_log().exit_state());
    } else {
        return "unknown";
    }
}
```
Chý ý rằng cách thay thế này không định nghĩ thêm bất cứ biến nào cả! Như đã giới thiệu ở Chương 9, Biến và khả năng đọc hiểu, các biến mà lưu trữ giá trị tạm thời thì không cần thiếu.

Với cách này, bạn chỉ đơn giản "cắt" vấn đề thành một hướng khác. Cả hai cách đều rất dễ đọc hiểu, vì chúng yêu cầu người đọc chỉ cần nghĩ về một đầu việc tại mỗi thời điểm

## Tổng kết
Chương này giới thiệu một kĩ thuật đơn giản để tổ chức mã nguồn của bạn: chỉ làm một việc tại một thời điểm.

Nếu bạn có mã nguồn mà khó cho việc đọc hiểu, thì hãy cố gắng liệt kê ra các đầu việc mà nó đang làm. Một vài trong số đó sẽ dễ dàng trở thành các functions (hoặc classes) độc lập. Một số khác thì sẽ là các đoạn logic hoàn thiện trong function. Chi tiết chính xác cách bạn chia nhỏ chúng không quan bằng việc chúng đã đươc chia nhỏ. Phần khó khăn nhất là mô tả chính xác các việc mà bạn đang cần làm.
