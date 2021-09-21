# BTree

B-Tree là cây tìm kiếm tự cân bằng và là dạng tổng quát của cây nhị phân tìm kiếm trong đó 1 node có thể có nhiều hơn 1 phần tử. Không như các cây tìm kiếm tự cân bằng khác như AVL tree hay Redblack tree được sử dụng chủ yếu trong bộ nhớ RAM, BTree được sinh ra do nhu cầu lưu trữ và tìm kiếm dữ liệu trên bộ nhớ cứng (hard disk). Với bộ nhớ cứng chi phí 1 lần truy cập dữ liệu là rất lớn và mỗi lần truy cập dữ liệu sẽ được đọc lên theo block nhiều dữ liệu 1 lần nên Btree giúp giải quyết 2 vấn đề:

- Mỗi node có nhiều phần tử phù hợp với việc đọc dữ liệu trên đĩa cứng 1 lần theo block chứa nhiều bản ghi dữ liệu.
- Vì mỗi node có nhiều phần tử nên độ cao của cây giảm khiến cho số lần truy cập đĩa cứng khi thao tác cũng giảm.

## Tính chất Btree

- Tất cả nút lá ở cùng độ sâu.
- Với các nút không phải là lá (internal node), gọi bậc(**degree**) `d` của nút là số con của nút đó, thì `k = d - 1` là số khóa (key) của nút đó.
- Gọi **order** (maximum degree) của cây là `2t`, ta có `t <= d <= 2t`.
- Số khóa `k` nhỏ nhất của mọi nút không phải gốc là `t - 1`, với nút gốc số khóa `k >= 0`.
- Số khóa `k` lớn nhất của mọi nút kể cả gốc là `2t - 1`.
- Tất cả khóa của 1 nút được sắp xếp tăng dần, cây con nằm giữa 2 khóa `k1` và `k2` chứa tất cả các khóa nằm trong khoảng `k1 -> k2`.**

Btree với **order** 4 (số nút con lớn nhất `2t` là 4, số khóa lớn nhất 3, nhỏ nhất là 1) có dạng như sau.

```[tree](shape=rect,size=30,height=200)
(3)
(1,2)(4,5,6,8)
```

Trên hình ta có khóa là các phần tử nằm trong hình vuông, số con là số đường link đi ra từ nút.

Độ phức tạp tính toán các thao tác Btree.

## Thao tác thêm (Insert)

1. Phần tử mới luôn được thêm trực tiếp ở nút lá. Ta xuất phát từ gốc, dựa vào giá trị phần tử muốn thêm đi vào nhánh cây con phù hợp cho đến khi tìm được vị trí phù hợp ở 1 nút lá.

2. Thêm phần tử mới vào nút lá ở vị trí tìm được.

3. Lúc này ta kiểm tra số phần tử ở nút mới thêm nếu vượt quá số phần tử tối đa cho phép, thực hiện chia tách nút này thành 2 nút

    3.1. Chọn ra phần tử ở giữa nút gọi là phần tử chia.
    3.2. Các phần tử bên trái phần tử chia giữ nguyên vị trí, chuyển các phần tử bên phải phần tử chia sang 1 nút được tạo mới. Trong trường hợp là nút trong ta chia các phần tử con tương ứng.

4. Chuyển phần tử chia lên trên nút cha ở vị trí nút con đang xét. Thêm nút mới vào tập con của nút cha, ở vị trí bên cạnh nút hiện tại. Khi này số phần tử ở nút cha tăng thêm 1. Ta tiếp tục lặp lại bước 3 đối với nút cha trong trường hợp số phần tử của nút cha vượt quá số phần tử tối đa. Nếu không ta chuyển qua bước 5.

5. Trong trường hợp nút gốc bị chia, do không tồn tại nút cha ta tạo 1 nút mới bao gồm 1 phần tử chia và 2 con là nút gốc hiện tại và nút mới chia từ nút gốc. Sau đó cập nhật nút gốc là nút mới này.

Với **order** 5 ta có

- Số phần tử nhỏ nhất: 2
- Số phần tử lớn nhất: 4

```[tree](shape=rect,size=30,height=200)
(A,B,C,D)
(...)(E,F,G,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,B,C,D)
(...)(E,F,G,I{"c":"green"},H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,G{"c":"green"},B,C,D)
(...)(E,F)(I,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(B){"c":"green","t":"gốc mới"}
(A,G{"t":"gốc cũ"})(C,D)
(...)(E,F)(I,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```
