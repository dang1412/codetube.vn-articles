# Cây nhị phân tìm kiếm (Binary Search Tree)

## Giới thiệu cây nhị phân (Binary Tree)

Cấu trúc dữ liệu cây (tree) bao gồm 1 nút gốc (root) và các nút con (children), các nút con lại bao gồm nút con khác của nó, tạo thành cấu trúc dữ liệu dạng phân cấp (hierachy) bắt đầu từ gốc và phình ra ở dưới (bottom).

Cây nhị phân là cấu trúc dữ liệu dạng cây trong đó mỗi nút có tối đa 2 nút con, đây là cấu trúc dữ liệu phổ biến và rất hữu dụng trong khoa học máy tính. Ví dụ cây nhị phân có 8 nút được biểu diễn như hình sau:

```[tree](size=30,height=200,width=300)
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

```[tree](size=30,height=200,width=300)
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

```[tree](size=30,height=200,width=300)
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

```[tree](size=30,height=200)
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

```[tree](size=30,height=200,width=300)
(A)
(B)(C)
(D)(E)
```

Cây không cân bằng

```[tree](size=30,height=200,width=300)
(A)
(B)(C)
(D)()()(E)
(F)()
```

## Cách biểu diễn (Representation)

```[tree](size=30,height=200,width=300)
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

```[tree](size=30,height=200)
(50)
(30)(70)
(23)(35)()(80)
(11)(25)(31)(42)(73){"p": "80"}(85){"p": "80"}
```

Độ cao trung bình của 1 cây nhị phân được sinh ra ngẫu nhiên là O(log n), do đó thời gian thao tác trung bình trên cây nhị phân là O(log n). Tuy nhiên có những trường hợp độ cao cây nhị phân có thể lớn hơn nhiều, trong trường hợp xấu nhất độ cao cây có thể là O(n) do đó thời gian thao tác cũng là O(n). Chúng ta xem xét các ví dụ sau:

```[tree](size=30,height=300,width=300)
(50)
(43)(54)
(38)()
(31)()
(20)()
```

```[tree](size=30,height=300,width=300)
(33)
()(42)
(){"p":"42"}(45){"p":"42"}
(){"p":"45"}(57){"p":"45"}
(48){"p":"57"}(66){"p":"57"}
```

<!-- ## Ứng dụng

Để quản lý 1 tập các phần tử có thứ tự (được sắp xếp theo thứ tự để tiện lợi trong thao tác thêm phần tử mới vào trong tập và lấy ra phần tử mong muốn), chúng ta có các kiểu dữ liệu cơ bản đó là:

- Mảng tuyến tính (array)
- Danh sách liên kết (linked list)

Cả 2 cách lưu trữ này đều có ưu điểm và nhược điểm riêng với các thao tác nhất định trong việc quản lý tập có thứ tự:

|                    | Ưu điểm                                   | Nhược điểm                                                                          |
|--------------------|-------------------------------------------|-------------------------------------------------------------------------------------|
| Mảng               | Tìm kiếm nhanh bằng binary search O(logn) | Thêm và xóa chậm do phải dịch chuyển toàn bộ các phần tử phía sau điểm cần sửa O(n) |
| Danh sách liên kết | Thêm hay xóa 1 phần tử tức thì O(1)       | Thao tác tìm kiếm chậm do phải duyệt qua từng phần tử từ đầu O(n)                   |

Từ đó cây nhị phân tìm kiếm được thiết kế kết hợp hài hòa ưu điểm của 2 kiểu dữ liệu trên để trở thành cấu trúc dữ liệu hiệu quả trong việc lưu trữ và truy xuất tập các phần tử có thứ tự. Các phần tử trong cây nhị phân tìm kiếm được sắp xếp theo thứ tự nhất định, các thao tác cơ bản như thêm, xóa và tìm kiếm đều có thể được thực hiện hiệu quả trong thời gian tính toán có độ phức tạp O(logn) trong đó n là số lượng các phần tử trong tập. -->

## Thao tác (operations)

| No | Thao tác             | Trường hợp trung bình | Trường hợp xấu nhất |
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

Minh họa quá trình tìm kiếm số **6** trong cây nhị phân tìm kiếm như sau:

~~~[tree](shape=circle,size=34,height=200,width=300)
(8)
(4)(15)
(2)(6)(9)(16)

(8){"c": "red", "f": 6}
(4)(15)
(2)(6)(9)(16)

(8){"o": 0.3}
(4){"c": "red","f": 6}(15){"o": 0.3}
(2)(6)(9){"o": 0.3}(16){"o": 0.3}

(8){"o": 0.3}
(4){"o": 0.3}(15){"o": 0.3}
(2){"o": 0.3}(6){"c": "red", "t": "tìm thấy"}(9){"o": 0.3}(16){"o": 0.3}
~~~

Dễ thấy với cây nhị phân tìm kiếm cân bằng như trên với mỗi phép so sánh ta loại trừ được 1 nửa số phần tử cần tìm.

### Thêm (insert)

Ví dụ ta thêm lần lượt các phần tử sau vào cây nhị phân rỗng ban đầu:

`9 4 6 20 70 15 1`


~~~[tree](shape=circle,size=34,height=200)
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
~~~

### Xóa (delete)
