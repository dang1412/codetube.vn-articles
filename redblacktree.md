# Cây tự cân bằng đỏ đen (Red Black Tree)

Cây đỏ đen (red black tree) là cây nhị phân tìm kiếm được ràng buộc thêm bởi 1 số điều kiện (constraint) để đảm bảo cây luôn ở trạng thái tương đối cân bằng (độ dài giữa các nhánh cây không chênh lệch nhau quá lớn), nhằm tối đa hóa hiệu quả các thao tác tìm kiếm và lưu trữ trên cây.

1 dạng khác của cây nhị phân tự cân bằng là cây AVL, tuy nhiên điều kiện cân bằng của cây đỏ đen được thiết kế lỏng hơn so với cây AVL. Ở cây AVL độ dài 2 nhánh cây chênh nhau không quá 1 đơn vị thì ở cây đỏ đen 1 nhánh cây có độ dài không quá 2 lần nhánh còn lại (với mọi nút). Do tính chất lỏng hơn này trong trường hợp thực hiện nhiều lần các thao tác thêm và xóa ở cây đỏ đen sẽ hiệu quả hơn so với cây AVL vì không phải tái cấu trúc cây quá nhiều lần.

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

## Cài đặt

Ta để ý nút đỏ mang ý nghĩa là cây đang lệch về phía nhánh cây chứa nó, từ đó ta hiểu được logic trong cách cài đặt cũng như dễ nhớ hơn.

### Thêm (Insert)



### Xóa (Delete)

Trước hết ta làm theo các bước xóa như ở trong cây nhị phân tìm kiếm thông thường để đưa về trường hợp xóa nút `x` là nút lá hoặc chỉ có 1 con (xem bài viết về cây nhị phân tìm kiếm).

Ý tưởng của thuật toán là kiểm tra cây đang lệch về phía nào bằng cách xem nút đỏ đang nằm ở đâu và cố gắng chuyển chỗ lệch đó về phía nhánh của phần tử muốn xóa. Hay nói cách khác, tìm cách **chuyển màu của nút muốn xóa thành đỏ** mà vẫn giữ tính chất cây đỏ đen bằng cách vận dụng các thao tác xoay trái, xoay phải và đổi màu, khi đó ta dễ dàng xóa phần tử mà vẫn giữ được tính cân bằng của cây.

Gọi S là nút anh em (sibling) của `x` và `P` là nút cha của `x`. Xét các trường hợp sau:

**Case 1** `x` là nút đỏ: trường hợp này ta xóa `x` như đối với cây nhị phân tìm kiếm thông thường vì nó ko làm ảnh hưởng đến tính chất 4 và 5 được nhắc ở trên.

**Case 2** `x` là nút đen và có nút con là đỏ: ta thay `x` bằng nút con đỏ của nó và đổi màu nút con đỏ thành đen. Thay 1 nút đen bằng 1 nút đen khác tính chất 4 và 5 được bảo toàn.

Trường hợp x là nút đen có 1 nút con là đen khác NULL ko tồn tại vì vi phạm tính chất 5.

```[tree](size=40)
(A){"c":"black","t":"x"}
(NULL){"c":"black","o":".6"}(B){"c":"black"}
(NULL){"c":"black","p":"B","o":".6"}(NULL){"c":"black","p":"B","o":".6"}
```

Nên ta có trường hợp tiếp theo `x` là nút màu đen và không có nút con màu đỏ suy ra `x` là nút lá.

- Nút cha của `x` `P` là đỏ (suy ra `x` và `S` là đen): 

3. `x` là nút lá đen và nút anh em `S` là đỏ

- `x` là nút lá màu đen, nút anh em `S` màu đen, cả 2 con của S là đen
  - Nút cha của x `P` là đỏ: đổi màu P -> đen, S -> đỏ, ta xóa `x` và điều kiện cây đỏ đen được bảo toàn.
  - Nút cha của x `P` là đen: lặp lại coi `P` là nút x mới

**Case 3**. `x` là nút lá màu đen

**Case 3.1** `S` là nút đen, nút con phải của `S` là đỏ.

Trường hợp này nút con trái của `S` (NULL hoặc đỏ), nút cha `P` có thể là đen hoặc đỏ đều được.

- Thực hiện phép xoay trái đối với `P`.
- Gán màu của `S` là màu hiện tại của `P`.
- Gán màu của `P` là đen.
- Gán màu con phải của `S` là đen.
- Gán màu của `x` là đỏ.
- Bài toán trở về trường hợp 1, ta đơn giản xóa `x`.

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

(D){"c":"pink","t":"S"}
(B){"t":"P","c":"black"}(E){"c":"black"}
()(C){"c":"pink"}
```

Trên hình minh họa ta nhận thấy nếu trạng thái 1 được đảm bảo tính chất cây đỏ đen thì trạng thái 3 (chưa xóa x) và trạng thái 4 (đã xóa x) cũng đảm bảo tính chất này.

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

**Case 3.2** `S` là nút đen, nút con trái của `S` là đỏ.

Ở đây chỉ cần xét trường hợp nút con phải của `S` là NULL (TH nút này là đỏ ta có case 3.1 ở trên, TH là đen khác NULL không xảy ra vì vi phạm tính chất 5).

- Đổi màu nút `S` sang đỏ, nút con trái của `S` sang đen.
- Thực hiện động tác xoay phải đối với S.
- Ta có cây trở về trường hợp 3.1 với con trái `S` (lúc chưa xoay) đóng vai trò là nút `S` mới, thực hiện tiếp các bước giống như ở 3.1.

Minh họa

```[tree](size=40)
(B){"c":"pink"}
(A){"c":"black","t":"x"}(D){"c":"black","t":"S"}
(C){"c":"red","p":"D"}(){"p":"D"}

(B){"c":"pink"}
(A){"c":"black","t":"x"}(C){"c":"black","t":"new S"}
(){"p":"C"}(D){"c":"red","t":"S","p":"C"}

(C){"c":"black","t":"new S"}
(B){"c":"pink"}(D){"c":"red","t":"S"}
(A){"c":"black","t":"x"}()

(C){"c":"pink","t":"new S"}
(B){"c":"black"}(D){"c":"black","t":"S"}
(A){"c":"red","t":"x"}()

(C){"c":"pink","t":"new S"}
(B){"c":"black"}(D){"c":"black","t":"S"}
()()
```

**Case 3.3** `S` là nút đen (2 nút con của `S` là NULL)

**Case 3.3.1** nếu nút cha `P` là đỏ

- Đổi màu `P` sang đen.
- Đổi màu `S` sang đỏ
- Đổi màu `x` sang đỏ
- Xóa `x`

```[tree](size=40)
(B){"c":"red"}
(A){"c":"black"}(D){"c":"black"}
```

## Chú ý

- Nút đỏ luôn có 0 hoặc 2 con
- 
