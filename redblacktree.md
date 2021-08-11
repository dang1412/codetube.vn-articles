# Cây tự cân bằng đỏ đen (Red Black Tree)

Cây đỏ đen (red black tree) là cây nhị phân tìm kiếm được ràng buộc thêm bởi 1 số điều kiện (constraint) để đảm bảo cây luôn ở trạng thái tương đối cân bằng (độ dài giữa các nhánh cây không chênh lệch nhau quá lớn), nhằm tối đa hóa hiệu quả các thao tác tìm kiếm và lưu trữ trên cây.

1 dạng khác của cây nhị phân tự cân bằng là cây AVL, tuy nhiên điều kiện cân bằng của cây đỏ đen được thiết kế lỏng hơn so với cây AVL. Ở cây AVL độ dài 2 nhánh cây chênh nhau không quá 1 đơn vị thì ở cây đỏ đen 1 nhánh cây có độ dài **không quá 2 lần** nhánh còn lại (với mọi nút). Do tính chất lỏng hơn này trong trường hợp thực hiện nhiều lần các thao tác thêm và xóa ở cây đỏ đen sẽ hiệu quả hơn so với cây AVL vì không phải tái cấu trúc cây quá nhiều lần.

## Tính chất cây đỏ đen

1. Mọi nút đều có thuộc tính là đỏ hoặc đen.
2. Nút gốc là nút đen.
3. Tất cả nút NULL là nút đen.
4. Nếu 1 nút là đỏ thì tất cả nút con của nó phải là đen, hay nói cách khác không có 2 nút đỏ liên tiếp trên 1 đường đi từ gốc đến lá.
5. Với mọi đường đi từ gốc đến NULL, số nút đen là bằng nhau.

Ở đây 2 thuộc tính chính yếu để tạo nên tính chất cân bằng cho cây đỏ đen là thuộc tính 4 và 5. Ta sử dụng 2 thuộc tính này để chứng minh tính cân bằng của cây đỏ đen.

## Tính cân bằng của cây đỏ đen

Dựa vào tính chất 5, với mọi đường đi từ gốc đến NULL số nút đen là bằng nhau, gọi số nút đen này là `bh` (không tính NULL). Trong trường hợp không có nút đỏ nào ta có cây nhị phân hoàn hảo (Perfect Binary Tree) có tổng số nút `n = 2^bh - 1`. Trong trường hợp có thêm các nút đỏ mà vẫn đảm bảo tính chất này ta có tổng số nút **n >= 2^bh - 1**.

Gọi độ cao cây là h, dựa vào tính chất 4 do không có 2 nút đỏ liên tiếp trên đường đi từ gốc đến NULL ta có **bh >= h/2**, vì nếu bh < h/2 ta bắt buộc phải có 2 nút đỏ liên tiếp.

Tổng hợp 2 ý trên ta có `n >= 2^bh -1 >= 2^(h/2) - 1`. Suy ra:

`log(n + 1) >= h/2` hay **h <= 2log(n + 1)**.

Suy ra độ phức tạp tính toán của cây nhị phân là O(h) hay chính là O(logn).

## Các thao tác tái cấu trúc cây

Ngoài các thao tác chính của cây nhị phân tìm kiếm (Thêm, xóa), chúng ta sử dụng các công cụ sau để tái cấu trúc cho cây khi cần thiết.

### Xoay trái (Left-rotation) và xoay phải (Right-rotation)

Từ trạng thái 1 -> trạng thái 2 ta có thao tác xoay trái đối với x. Ngược lại từ 2 -> 1 ta có thao tác xoay phải đối với y.

```[tree](size=34)
(P)
(x){"c":"green"}
(a){"c":"black"}(y){"c":"red"}
(b){"p":"y","c":"black"}(c){"p":"y","c":"black"}

(P)
(y){"c":"red"}
(x){"c":"green"}(c){"c":"black"}
(a){"c":"black"}(b){"c":"black"}
```

Phần cây xoay có thể là con trái hoặc con phải của P.

### Đổi màu (Flip-color)

Đổi màu 1 nút từ đỏ thành đen hoặc ngược lại.

## Các thao tác

Cây đỏ đen có các thao tác thêm, xóa, tìm kiếm, tìm phần tử trước, phần tử sau, duyệt... giống như ở cây nhị phân thông thường. Ở đây ta đi vào phần cài đặt 2 thao tác chính mà đòi hỏi cây đỏ đen tự tái cấu trúc nó để đạt được sự cân bằng:

- Thêm (Insert)
- Xóa (Delete)

Ta để ý nút đỏ mang ý nghĩa là cây đang lệch về phía nhánh cây chứa nó, từ đó ta hiểu được logic trong cách cài đặt cũng như dễ nhớ hơn.

## Thao tác thêm (Insert)

Trước hết thêm phần tử vào cây như ở cây nhị phân tìm kiếm thông thường và gán cho nút mới này màu đỏ. Sau đó ý ta kiểm tra xem liệu có 2 phần tử màu đỏ liên tiếp xảy ra vì thao tác này không (2 nút đỏ liên tiếp mang ý nghĩa là việc thêm phần tử làm cây lệch nhiều hơn mức cho phép về phía đó). Nếu có ta sửa nó bằng cách vận dụng các thao tác xoay cây và đổi màu.

Gọi cây hiện tại `T`, nút mới thêm `K`, nút cha của nút mới thêm `P`, nút ông (grandparent) `G` và nút chú (uncle - nút anh em của nút cha) `U`. Xét các trường hợp sau:

### Case 1: `K` là gốc (không tồn tại nút cha `P`)

Nếu `K` là gốc và màu đỏ, ta chuyển màu `K` thành đen. Trường hợp này độ cao màu đen `bh` của cây tăng thêm 1.

> Thuật toán gọi vào đây trong 2 trường hợp:
>
> - Thêm K vào cây rỗng
> - Gọi đệ qui từ dưới lên ở trường hợp `3.1` (được nhắc đến ở dưới)

### Case 2: Nút cha `P` màu đen

Nếu nút cha `P` của `K` màu đen, và `K` được set là đỏ nên thao tác thêm này không làm ảnh hưởng tính chất cây đỏ đen nên không phải làm gì thêm.

### Case 3: Nút cha `P` màu đỏ

Khi này ta có 2 nút đỏ liên tiếp `P` và `K`. `P` màu đỏ suy ra nút ông `G` màu đen vì T đảm bảo tính chất cây đỏ đen trước khi thêm `K`. Để sửa tình huống này ta cần xét nút chú `U` là đỏ hay đen.

**Case 3.1** `P` màu đỏ và `U` cũng màu đỏ: tình huống này ta đổi màu `P` thành đen, `U` thành đen và `G` thành đỏ. Khi này số nút đen của 2 nhánh không đổi và 2 nút đỏ liên tiếp của phần cây con `GPUK` được sửa.

```[tree](size=40)
(A)
(B){"c":"black","t":"G"}
(C){"c":"red","t":"U"}(D){"c":"red","t":"P"}
(){"p":"D"}(E){"p":"D","c":"red","t":"K"}

(A)
(B){"c":"red","t":"G"}
(C){"c":"black","t":"U"}(D){"c":"black","t":"P"}
(){"p":"D"}(E){"p":"D","c":"red","t":"K"}
```

Do `G` chuyển màu từ đen sang đỏ có khả năng gặp 1 nút đỏ liên tiếp ở trên, ta tiếp tục thực hiện thao tác kiểm tra và sửa nếu cần thiết lên trên với `G` đóng vai trò là `K` (gọi đệ qui lặp lại từ **case 1** đối với nút ông `G`).

**Case 3.2** `P` màu đỏ và `U` màu đen (hoặc NULL): trường hợp này ta có thể giải quyết bằng 1 hoặc 2 phép xoay cây tùy thuộc vào `P` và `K` là nút con trái hay phải.

**Case 3.2.1** `P` là con phải của `G`, `K` là con phải của `P`:

- Thực hiện phép xoay trái đối với `G`.
- Đổi màu `P` thành đen, `G` sang đỏ.

```[tree](size=40)
(A)
(B){"c":"black","t":"G"}
(C){"c":"black","t":"U"}(D){"c":"red","t":"P"}
(F){"p":"D","c":"black"}(E){"p":"D","c":"red","t":"K"}

(A)
(D){"c":"black","t":"P"}
(B){"c":"red","t":"G"}(E){"c":"red","t":"K"}
(C){"c":"black","t":"U"}(F){"c":"black"}
```

**Case 3.2.2** `P` là con phải của `G`, `K` là con trái của `P`:

- Thực hiện phép xoay phải đối với `P`.
- Trở về case 3.2.1 (với `K` và `P` đổi vai trò cho nhau).

```[tree](size=40)
(A)
(B){"c":"black","t":"G"}
(C){"c":"black","t":"U"}(D){"c":"red","t":"P"}
(E){"p":"D","c":"red","t":"K"}(F){"p":"D","c":"black"}

(A)
(B){"c":"black","t":"G"}
(C){"c":"black","t":"U"}(E){"p":"D","c":"red","t":"K"}
(G){"p":"E","c":"black","t":"E's child"}(D){"c":"red","t":"P","p":"E"}
```

**Case 3.2.3** `P` là con trái của `G`, `K` là con trái của `P`, trường hợp này ngược lại với 3.2.1:

- Thực hiện phép xoay phải đối với `G`.
- Đổi màu `P` thành đen, `G` sang đỏ.

```[tree](size=40)
(A)
(B){"c":"black","t":"G"}
(C){"c":"red","t":"P"}(D){"c":"black","t":"U"}
(E){"c":"red","t":"K"}(F){"c":"black"}

(A)
(C){"c":"black","t":"P"}
(E){"c":"red","t":"K"}(B){"c":"red","t":"G"}
(F){"c":"black","p":"B"}(D){"c":"black","t":"U","p":"B"}
```

**Case 3.2.4** `P` là con trái của `G`, `K` là con phải của `P`, trường hợp này ngược lại với 3.2.2:

- Thực hiện phép xoay trái đối với `P`.
- Trở về case 3.2.3 (với `K` và `P` đổi vai trò cho nhau).

### Tóm tắt thuật toán insert

- Thêm nút mới và gán màu của nút mới là đỏ.
- Sửa tình huống có 2 nút đỏ liên tiếp nếu nút cha cũng là đỏ bằng cách xem nhánh đối diện:
  - Nếu nút cần sửa là gốc (không có cha) thì chuyển màu đỏ sang đen.
  - Nếu nút chú là màu đen ta có thể sửa sự lệch của cây con đang xét bằng phép xoay cây sang phía nhánh của nút chú.
  - Nếu nút chú màu đỏ ta đổi màu nút cha và chú thành đen, nút ông thành đỏ rồi tiếp tục gọi đệ qui lên trên (đối với nút ông) để sửa nút đỏ này nếu cần.

## Thao tác xóa (Delete)

Trước hết ta làm theo các bước xóa như ở trong cây nhị phân tìm kiếm thông thường để đưa về trường hợp xóa nút `x` là nút lá hoặc chỉ có 1 con (xem phần xóa trong bài về cây nhị phân tìm kiếm).

Ý tưởng của thuật toán là kiểm tra cây đang lệch về phía nào bằng cách xem nút đỏ đang nằm ở đâu quanh chỗ nút muốn xóa và cố gắng chuyển chỗ lệch đó về phía nhánh của phần tử này. Hay nói cách khác, tìm cách **chuyển màu của nút muốn xóa thành đỏ** mà vẫn giữ tính chất cây đỏ đen bằng cách vận dụng các thao tác xoay trái, xoay phải và đổi màu, khi đó ta dễ dàng xóa phần tử mà vẫn giữ được tính cân bằng của cây.

Gọi S là nút anh em (sibling) của `x` và `P` là nút cha của `x`. Xét các trường hợp sau:

### Case 1 `x` là nút đỏ

Trường hợp này ta xóa `x` như đối với cây nhị phân tìm kiếm thông thường vì nó ko làm ảnh hưởng đến tính chất 4 và 5 được nhắc ở trên.

### Case 2 `x` là nút đen và có nút con là đỏ

Ta thay `x` bằng nút con đỏ của nó và đổi màu nút con đỏ thành đen. Thay 1 nút đen bằng 1 nút đen khác tính chất cây đỏ đen được bảo toàn.

> Trường hợp x là nút đen có 1 nút con NULL và 1 nút con là đen khác NULL ko tồn tại vì vi phạm tính chất 5.

### Case 3 `x` là nút màu đen

Khi `x` là nút màu đen, ta tìm cách chuyển `x` thành đỏ bằng các thao tác xoay cây và đổi màu. Để giản lược ta xét trường hợp `x` là con trái với 4 trường hợp con sau (với trường hợp `x` là con phải ta làm giống hệt nhưng ngược lại động tác xoay):

**Case 3.1** `S` là nút đen, nút con phải của `S` là đỏ.

Trường hợp này nút con trái của `S`, nút cha `P` có thể là đen hoặc đỏ đều được.

- Thực hiện phép xoay trái đối với `P`.
- Gán màu của `S` là màu hiện tại của `P`.
- Gán màu của `P` là đen.
- Gán màu con phải của `S` là đen.
- Gán màu của `x` là đỏ.

Minh họa, với màu hồng nghĩa là màu đỏ hoặc đen đều được:

```[tree](size=40)
(B){"t":"P","c":"pink"}
(A){"c":"black","t":"x"}(D){"c":"black","t":"S"}
(C){"p":"D","c":"pink"}(E){"p":"D","c":"red"}

(D){"c":"black","t":"S"}
(B){"t":"P","c":"pink"}(E){"c":"red"}
(A){"c":"black","t":"x"}(C){"c":"pink"}

(D){"c":"pink","t":"S"}
(B){"t":"P","c":"black"}(E){"c":"black"}
(A){"c":"red","t":"x"}(C){"c":"pink"}
```

Trên hình minh họa ta nhận thấy nếu trạng thái 1 được đảm bảo tính chất cây đỏ đen thì trạng thái 3 cũng đảm bảo tính chất này.

Trạng thái 1:

```[tree](size=40)
(B){"t":"P","c":"pink"}
(A){"c":"black","t":"x"}(D){"c":"black","t":"S"}
(C){"p":"D","c":"pink"}(E){"p":"D","c":"red"}
```

Trạng thái 3:

```[tree](size=40)
(D){"c":"pink","t":"S"}
(B){"t":"P","c":"black"}(E){"c":"black"}
(A){"c":"red","t":"x"}(C){"c":"pink"}
```

Ta thấy nút `x` được chuyển từ màu đen sang đỏ, ta xem xét khả năng con của `x` có thể là đỏ dẫn đến 2 nút đỏ liên tiếp không:

- Case 3 này xuất phát từ việc đổi màu `x` là nút lá màu đen, trong trường hợp này đổi sang đỏ không tạo thành 2 nút đỏ liên tiếp.
- Nếu là trường hợp gọi đệ qui từ dưới lên nhằm chuyển `x` thành đỏ, ta chỉ gọi đến đoạn này trong trường hợp `x` là đen và cả 2 con của `x` cũng là đen (cụ thể là trường hợp 3.3.2 ở dưới). Nên việc đổi màu `x` sang đỏ cũng ok.

Như vậy điều kiện không có 2 nút đỏ liên tiếp được đảm bảo, xét về số nút đen:

- ..BA.. trạng thái 1 = ..DBA.. trạng thái 3
- ..BDC.. trạng thái 1 = ..DBC.. trạng thái 3
- ..BDE.. trạng thái 1 = ..DE.. trạng thái 3

Như vậy nếu trạng thái 1 đảm bảo tính chất về số nút đen, thì trường hợp 3 cũng đảm bảo tính chất này. Các case sau ta cũng phân tích tương tự để thấy tính chất cây đỏ đen được đảm bảo sau các thao tác tái cấu trúc cây.

**Case 3.2** `S` là nút đen, nút con trái của `S` là đỏ.

Ta chỉ xét trường hợp nút con phải của `S` là đen (hoặc NULL), nút cha `P` có thể là đen hoặc đỏ đều được.

- Đổi màu nút `S` sang đỏ, nút con trái của `S` sang đen.
- Thực hiện động tác xoay phải đối với S.
- Ta có cây trở về trường hợp 3.1 với con trái `S` (lúc chưa xoay) đóng vai trò là nút `S` mới, thực hiện tiếp các bước giống như ở 3.1.

```[tree](size=40)
(B){"c":"pink","t":"P"}
(A){"c":"black","t":"x"}(D){"c":"black","t":"S"}
(C){"c":"red","p":"D"}(E){"p":"D","c":"black"}

(B){"c":"pink","t":"P"}
(A){"c":"black","t":"x"}(C){"c":"black","t":"new S"}
(F){"p":"C","c":"pink","t":"left child"}(D){"c":"red","t":"S","p":"C"}
(){"p":"D"}(E){"p":"D","c":"black"}

(C){"c":"black","t":"new S"}
(B){"c":"pink","t":"P"}(D){"c":"red","t":"S"}
(A){"c":"black","t":"x"}(F){"c":"pink"}

(C){"c":"pink","t":"new S"}
(B){"c":"black","t":"P"}(D){"c":"black","t":"S"}
(A){"c":"red","t":"x"}(F){"c":"pink"}
```

**Case 3.3** `S` là nút đen, cả 2 con của `S` là đen

**Case 3.3.1** nếu nút cha `P` là đỏ

- Đổi màu `P` sang đen.
- Đổi màu `S` sang đỏ
- Đổi màu `x` sang đỏ

```[tree](size=40)
(B){"c":"red","t":"P"}
(A){"c":"black","t":"x"}(D){"c":"black","t":"S"}
(C){"c":"black","p":"D"}(E){"c":"black","p":"D"}

(B){"c":"black","t":"P"}
(A){"c":"red","t":"x"}(D){"c":"red","t":"S"}
(C){"c":"black","p":"D"}(E){"c":"black","p":"D"}
```

**Case 3.3.2** nếu nút cha `P` là đen

Trường hợp này nhánh cây con đang xét toàn là đen ta cần gọi đệ qui lên trên nhằm chuyển phần lệch về phía cây con đang xét (đổi màu nút cha `P` sang đỏ).

- Ta lặp lại từ đầu case 3 với nhiệm vụ đổi màu nút cha `P` sang đỏ (`P` đóng vai trò là nút `x` mới).
- Với nút cha `P` đỏ, tiếp tục thực hiện các bước như ở 3.3.1.

**Case 3.4** `S` là nút màu đỏ

Lúc này cả 2 con của `S` là màu đen (theo tính chất 4)

- Thực hiện thao tác xoay trái đối với `P`.
- Đổi màu `S` thành đen, `P` thành đỏ.
- Lúc này bài toán trở về 3.1, 3.2 hoặc 3.3.1 (`x` màu đen, nút cha `P` màu đỏ và `S` mới màu đen).

```[tree](size=40)
(B){"c":"black","t":"P"}
(A){"c":"black","t":"x"}(D){"c":"red","t":"S"}
(C){"c":"black","p":"D"}(E){"c":"black","p":"D"}

(D){"c":"red","t":"S"}
(B){"c":"black","t":"P"}(E){"c":"black"}
(A){"c":"black","t":"x"}(C){"c":"black"}

(D){"c":"black","t":"S"}
(B){"c":"red","t":"P"}(E){"c":"black"}
(A){"c":"black","t":"x"}(C){"c":"black","t":"new S"}
```

Kết thúc **case 3**: sau khi thành công chuyển màu của `x` sang đỏ, thực hiện xóa `x` (`x` khi này là nút lá).

### Tóm tắt thuật toán xóa

- Thực hiện các bước xóa ban đầu giống với cây nhị phân tìm kiếm thông thường, đưa về TH xóa `x` là nút lá hoặc chỉ có 1 con (ít nhất 1 nút con của `x` là NULL).
- Nếu nút cần xóa `x` là màu đỏ bỏ qua các bước sau để đến bước cuối cùng (xóa `x` khỏi cây).
- Nếu `x` là màu đen ta tìm cách chuyển màu `x` sang đỏ, hay nói cách khác là chuyển dịch sự lệch của cây sang nhánh muốn xóa (đỏ tượng trưng cho nhánh cây dài hơn). Ta tìm nút đỏ ở  lần lượt các vị trí sau (đến khi tìm đc thì dừng):
  - Con của `x`.
  - Nút anh em (sibling) của `x`.
  - Con của nút anh em.
  - Cha của `x`.
- Nếu không tìm thấy đỏ ở các vị trí trên, ta tìm cách chuyển màu nút cha của `x` sang màu đỏ (bằng cách gọi đệ quy bước 2 nhưng là đối với cha của `x`).
- Khi đã tìm được đỏ ở 1 trong các vị trí này có thể áp dụng các biện pháp xoay cây và đổi màu để chuyển `x` sang đỏ mà vẫn giữ được tính chất của cây.
- Xóa `x` khỏi cây như ở cây nhị phân tìm kiếm thông thường.

## Minh họa

Các bạn có thể thử tất cả các trường hợp trên bằng cách thêm hoặc xóa các phần tử tùy ý trên cây đỏ đen sau và kiểm chứng từng bước hoạt động in action.

```[rbtreevisual](size=40,height=320)
31 30 23 50 45 48 70 67 75
```
