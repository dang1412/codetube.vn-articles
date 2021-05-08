# Cây nhị phân tìm kiếm (Binary Search Tree)

## Giới thiệu cây nhị phân (Binary Tree)

Cấu trúc dữ liệu cây (tree) bao gồm 1 nút gốc (root) và các nút con (children), các nút con lại bao gồm nút con khác của nó, tạo thành cấu trúc dữ liệu dạng phân cấp (hierachy) bắt đầu từ gốc và phình ra ở dưới (bottom).

Cây nhị phân là cấu trúc dữ liệu dạng cây trong đó mỗi nút có tối đa 2 nút con, đây là cấu trúc dữ liệu phổ biến và rất hữu dụng trong khoa học máy tính. Ví dụ cây nhị phân có 8 nút được biểu diễn như hình sau:

```[tree](size=34,height=200,width=300)
(A)
(B)(C)
(D)(E)()(F)
(G){"p": "F"}(H){"p": "F"}
```

Với mỗi nút ta có thể có nút con trái và/hoặc nút con phải, trong trường hợp chỉ có 1 nút con ta có thể coi nó là nút con trái.

- Nút gốc (root) là nút bắt đầu của cây.
- Nút con bên trái là gốc của cây con trái (left-subtree), nút con bên phải là gốc của cây con phải (right-subtree).
- Nút lá (leaf node) là nút không có bất kỳ nút con nào.
- Nút trong (internal node) là nút có ít nhất 1 nút con.
- Chiều cao (height) của cây là độ dài đoạn đường dài nhất đi từ nút gốc đến nút lá (trong ví dụ trên độ cao cây là 4, A -> G hoặc H).

## Các trạng thái cây nhị phân

Để tiện lợi chúng ta gọi tên các trạng thái của cây nhị phân như sau

### Cây nhị phân đầy đủ (Full Binary Tree)

Mỗi nút có chính xác 0 hoặc 2 nút con.

```[tree](size=34,height=200,width=300)
(A)
(B)(C)
(D)(E)(F)(G)
(H){"p": "E"}(I){"p": "E"}
```

Tính chất:

- Số nút lá = số nút trong + 1

### Cây nhị phân hoàn thiện (Complete Binary Tree)

- Trừ tầng cuối, mỗi tầng của cây đều được lấp đầy các nút
- Ở tầng cuối các nút được lấp đầy theo thứ tự trái qua phải

```[tree](size=34,height=200,width=300)
(A)
(B)(C)
(D)(E)(F)(G)
(H)(I)(J)()
```

Tính chất:

- Tầng cuối của cây có thể có số nút từ 1 đến 2^h với h là độ cao cây.
- Số lượng nút trong = floor(n/2) với n là tống số nút.

### Cây nhị phân hoàn hảo (Perfect Binary Tree)

Là cây nhị phân trong đó tất cả nút trong đều có 2 nút con và tất cả nút lá đều ở cùng độ sâu.

```[tree](size=34,height=200)
(A)
(B)(C)
(D)(E)(F)(G)
(H)(I)(J)(K)(L)(M)(N)(O)
```

Tính chất

- Số nút lá = (n + 1) / 2, với n là tổng số nút.
- Tổng số nút n = 2 ^ (h + 1) - 1, với h là độ cao của cây.

### Cây cân bằng (Balanced Binary Tree)

Cây cân bằng là cây nhị phân trong đó với chính nó và mọi cây con (subtree) độ cao của cây con trái và độ cao của cây con phải sai khác nhau nhiều nhất là 1.

Ví dụ cây cân bằng

```[tree](size=34,height=200,width=300)
(A)
(B)(C)
(D)(E)
```

Cây không cân bằng

```[tree](size=34,height=200,width=300)
(A)
(B)(C)
(D)()()(E)
(F)()
```

## Cách biểu diễn (Representation)

```[tree](size=34,height=200,width=300)
(A)
(B)(C)
(D)(E)(F)()
(G){"p": "F"}(H){"p": "F"}
```

### Sử dụng mảng (array)

`[A,B,C,D,E,F,-,-,-,-,-,G,H,-,-]`

Khi dùng mảng chúng ta cần phân phối vùng nhớ cho `2^(h+1) - 1` phần tử với h là độ cao của cây. Cách biểu diễn dùng mảng thích hợp nhất đối với cây nhị phân hoàn thiện (Complete Binary Tree) khi sự lãng phí vùng nhớ là nhỏ nhất.

Nếu i là chỉ số của phần tử cha thì phần tử con trái nằm ở vị trí 2*i + 1, phần tử con phải nằm ở vị trí 2*i + 2.

### Sử dụng danh sách liên kết (linked list)

Với danh sách liên kết ta sử dụng cấu trúc dữ liệu như sau (`typescript`)

```ts
interface BinaryNode {
  value: string
  left: BinaryNode | null
  right: BinaryNode | null
}
```

## Cây nhị phân tìm kiếm (Binary Search Tree - BST)

Cây nhị phân tìm kiếm là cây nhị phân mà trong đó với chính nó và mọi cây con của nó

- Tất cả các phần tử ở bên trái gốc nhỏ hơn hoặc bằng nút gốc.
- Tất cả các phần tử ở bên phải gốc lớn hơn hoặc bằng nút gốc.

Ví dụ cây nhị phân tìm kiếm

```[tree](size=34,height=200)
(50)
(30)(70)
(23)(35)()(80)
(11)(25)(31)(42)(73){"p": "80"}(85){"p": "80"}
```

Độ cao trung bình của 1 cây nhị phân được sinh ra ngẫu nhiên là O(log n), do đó thời gian thao tác trung bình trên cây nhị phân là O(log n). Tuy nhiên có những trường hợp độ cao cây nhị phân có thể lớn hơn nhiều, trong trường hợp xấu nhất độ cao cây có thể là O(n) do đó thời gian thao tác cũng là O(n). Chúng ta xem xét các ví dụ sau:

```[tree](size=34,height=300,width=300)
(50)
(43)(54)
(38)()
(31)()
(20)()
```

```[tree](size=34,height=300,width=300)
(33)
()(42)
(){"p":"42"}(45){"p":"42"}
(){"p":"45"}(57){"p":"45"}
(48){"p":"57"}(66){"p":"57"}
```

## Thao tác (operations)

| No | Thao tác             | Thời gian trung bình  | Thời gian xấu nhất  |
|----|----------------------|-----------------------|---------------------|
| 1  | Tìm kiếm             | O(logn)               | O(n)                |
| 2  | Tìm phần tử nhỏ nhất | O(logn)               | O(n)                |
| 3  | Tìm phần tử lớn nhất | O(logn)               | O(n)                |
| 4  | Tìm phần tử trước    | O(logn)               | O(n)                |
| 5  | Tìm phần tử sau      | O(logn)               | O(n)                |
| 6  | Thêm                 | O(logn)               | O(n)                |
| 7  | Xóa                  | O(logn)               | O(n)                |

### Tìm kiếm (search)

Để tìm kiếm 1 phần tử trong cây nhị phân tìm kiếm ta bắt đầu từ gốc của cây:

1. Nếu gốc là null dừng quá trình tìm kiếm trả về kết quả **không tìm thấy**.
2. So sánh với phần tử gốc nếu bằng trả về kết quả **tìm thấy tại vị trí này**.
3. Nếu phần tử cần tìm nhỏ hơn gốc ta lặp lại bước 1 với cây con bên trái (loại trừ gốc và cây con phải).
4. Nếu phần tử cần tìm lớn hơn gốc ta lặp lại bước 1 với cây con bên phải (loại trừ gốc và cây con trái).

Minh họa quá trình tìm kiếm số **25** trong cây nhị phân tìm kiếm như sau:

```[tree](size=34,height=200,width=300)
(30)
(23)(35)
(11)(25)(31)(42)

(30){"c": "red", "f": "25"}
(23)(35)
(11)(25)(31)(42)

(30)
(23){"c": "red","f": "25"}(35)
(11)(25)(31)(42)

(30)
(23)(35)
(11)(25){"c": "red", "t": "tìm thấy"}(31)(42)
```

Với cây nhị phân tìm kiếm cân bằng như trên với mỗi phép so sánh ta loại trừ được 1 nửa số phần tử của tập cần tìm.

### Min và Max

- Phần tử tận cùng trái (left-most) của cây là phần tử nhỏ nhất
- Phần tử tận cùng phải (right-most) của cây là phần tử lớn nhất

### Tìm phần tử sau (Successor) và trước (Predecessor)

### Thêm (insert)

Ví dụ 1. Thêm số **24** vào cây nhị phân tìm kiếm

```[tree](size=34,height=220,width=300)
(30)
(23)(35)
(11)(25)(31)(42)

(30){"f": "24","c":"red"}
(23)(35)
(11)(25)(31)(42)

(30)
(23){"f": "24","c":"red"}(35)
(11)(25)(31)(42)

(30)
(23)(35)
(11)(25){"f": "24","c":"red"}(31)(42)

(30)
(23)(35)
(11)(25)(31)(42)
(24){"p":"25"}(){"p":"25"}
```

Ví dụ 2. Thêm lần lượt các phần tử sau vào cây nhị phân rỗng ban đầu:

`9 4 6 20 70 15 1`

```[tree](size=34,height=200)
()

(9)

(9){"c": "red", "f": "4"}

(9)
(4)()

(9){"c": "red", "f": "6"}
(4)()

(9)
(4){"c": "red", "f": "6"}()

(9)
(4)()
()(6)

(9){"c": "red", "f": "20"}
(4)()
()(6)

(9)
(4)(20)
()(6)

(9){"c": "red", "f": "70"}
(4)(20)
()(6)

(9)
(4)(20){"c": "red", "f": "70"}
()(6)

(9)
(4)(20)
()(6)()(70)

(9){"c": "red", "f": "15"}
(4)(20)
()(6)()(70)

(9)
(4)(20){"c": "red", "f": "15"}
()(6)()(70)

(9)
(4)(20)
()(6)(15)(70)

(9){"c": "red", "f": "1"}
(4)(20)
()(6)(15)(70)

(9)
(4){"c": "red", "f": "1"}(20)
()(6)(15)(70)

(9)
(4)(20)
(1)(6)(15)(70)
```

### Xóa (delete)
