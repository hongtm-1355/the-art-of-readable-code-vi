# CHAPTER ONE: Code should be easy to understand

![chapter_1_1](images/chapter_1_1.png)

Trong năm năm qua, chúng tôi đã thu thập hàng trăm ví dụ về code bẩn và phân tích lý do tại sao đống code đó lại không sạch, những nguyên tắc hay kỹ thuật nào để khiến chúng dễ đọc hơn. Chúng tôi nhận ra rằng tất cả các nguyên tắc đều bắt nguồn từ một xuất phát từ một điều cốt yếu: __Code nên được viết một cách dễ hiểu__

Chúng tôi tin rằng đây là nguyên tắc cơ bản nhưng quan trọng nhất bạn có thể sử dụng để quyết định cách viết code của mình. Thông qua quyển sách này, chúng tôi sẽ chỉ ra cách áp dụng nguyên tắc này vào những khía cạnh khác nhau trong cách bạn viết code hằng ngày. Nhưng viết code dễ hiểu là gì và tại sao code dễ hiểu lại quan trọng đến vậy?

## Điều gì làm cho những dòng code tốt hơn

Phần lớn những lập trình viên đều viết code dựa trên cảm tính và trực giác. Chúng ta đều biết cách viết code

```java
for (Node node = list.head; node != null; node = node.next){
    System.out.print(node.data);
}
```

sẽ tốt hơn cách viết code như sau:

```java
Node node = list.head;
if (node == null) return;
while (node.next != null) {
    System.out.print(node.data)
    node = node.next;
}
if (node != null) System.out.print(node.data);
```

Mặc dù cả hai cách trên đều thực hiện cùng một công việc in dữ liệu của một danh sách các node ra màn hình.

Trong hai đoạn code dưới đây, sẽ khó hơn để ta có thể chọn ra đâu mới là cách code tốt hơn

```java
return exponent >= 0 ? mantissa * (1 << exponent) : mantissa / (1 << -exponent);
```

```java
if (exponent >= 0) {
    return mantissa * (1 << exponent);
} else {
    return mantissa / (1 << -exponent);
}
```

Đoạn code thứ nhất nhìn gọn gàng hơn đoạn code thứ hai, tuy nhiên đoạn code thứ hai lại ít phức tạp hơn. Vậy đâu mới là tiêu chí quan trọng hơn? hay làm thế nào để quyết định cách viết code sao cho đúng?

## Nguyên tắc cơ bản về khả năng đọc

Sau khi nghiên cứu những đoạn code như trên, chúng tôi đi đến kết luận rằng thời gian đọc code chính là chỉ số quan trọng để đánh giá mức độ dễ đọc của một đoạn code. __Code nên được viết để giảm thiểu thời gian cần thiết để người khác hiểu nó__

Theo đúng nguyên tắc trên, nếu chọn ra một đồng nghiệp và xem người ấy mất bao nhiêu thời gian để đọc và hiểu đoạn code do bạn viết ra. Thời gian đọc hiểu này chính là tiêu chi bạn cần giảm thiểu. Ta cũng cần làm rõ hiểu ở đây là người khác có thể hiểu cách đoạn code thực thi, chỉnh sửa code, fix bug khi làm việc với code do bạn viết ra

## Có phải lúc nào ngắn hơn cũng tốt hơn?

Nói chúng, bạn viết càng ít code để giải quyết một vấn đề thì càng tốt. Thời gian để đọc 2000 dòng code có lẽ sẽ ít hơn thời gian dành ra để đọc hết 5000 dòng code.

Tuy nhiên không phải lúc nào viết code ngắn hơn cũng tốt hơn, một đoạn code như sau:

```C
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

nhìn sẽ rất phức tạp và mất nhiều thời gian để ta có thể hiểu nó, sẽ tốt hơn nếu ta tách logic của đoạn code trên như sau:

```C
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```

Đoạn code trên tuy dài hơn nhưng nhìn sẽ hiểu và tường minh hơn đoạn code ban đầu.

Cũng như vậy, các đoạn comment sẽ giúp ta __mất ít thời gian để hiểu__ mặc dù nó sẽ làm cho code của chúng ta dài hơn.

```C
// Fast version of "hash = (65599 * hash) + c"
hash = (hash << 6) + (hash << 16) - hash + c;
```

Mặc dù viết ít code là một mục tiêu tốt, nhưng sẽ tốt hơn nếu chúng ta viết code để người khác mất ít thời gian đọc nó.

## Nguyên tắc về thời gian đọc code có đi ngược lại với các mục tiêu khác?

Có thể bạn sẽ nghĩ, thế còn những khía cạnh khác như hiệu năng của code, kiến trúc mã nguồn, code có dễ để test,... thì sao? Liệu nguyên tắc trên có đang đi ngược lại các nguyên tắc khác?

Ngay cả với những đoạn code tối ưu nhất, vẫn có cách để khiến chúng trở nên dễ đọc hơn. Hơn nữa, việc viết code để dễ đọc còn giúp những đoạn code đó có kiến trúc tốt và dễ dàng để test hơn.

Phần còn lại của quyển sách này sẽ nói về việc làm thế nào để áp dụng nguyên tắc __viết code dễ đọc__ vào các tình huống khác nhau. Trong quyển sách này, viết code sao cho dễ đọc là nguyên tắc cốt lõi, vượt qua các nguyên tắc khác. Là một lập trình viên, chúng ta cũng nên tập luyện thói quen tái cấu trúc các đoạn code chưa hoàn hảo, hãy luôn tự hỏi: "Liệu đoạn code đó đã đễ đọc chưa?". Nếu chưa, hãy __make it easy to read and understanding__

## Phần khó khăn nhất

Để viết ra các đoạn mã nguồn tốt, bạn cần phải suy nghĩ xem liệu rằng đoạn code do mình viết ra đã đủ dễ hiểu đối với người khác chưa. Trước khi viết code, bạn cần thực sự đặt mình vào vị trí người đọc code. Nếu chấp nhận điều này, bạn sẽ trở thành một lập trình viên tốt hơn, ít bug hơn, đạt nhiều thành tựu trong công việc, có thể viết ra những đoạn code mà nhũng người xung quanh đều cảm thấy yêu thích khi sử dụng. Anyway, chúng ta cùng bắt đầu nào
