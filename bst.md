# Cây nhị phân tìm kiếm (Binary Search Tree)

Cây (Tree) là dạng cấu trúc dữ liệu cơ bản và hữu dụng trong lập trình. Cây bao gồm 1 nút gốc (root) và các nút con (children), các nút con lại bao gồm nút con khác của nó, tạo thành cấu trúc dữ liệu dạng phân cấp (hierachy) bắt đầu từ gốc và phình ra ở dưới (bottom). Dạng cây phổ biến hay gặp nhất đó chính là cây nhị phân tìm kiếm thỏa mãn các điều kiện sau:

- 1 nút có tối đa 2 nút con gọi là nút trái (left) và nút phải (right).
- Với mọi cây con, tất cả các phần tử ở bên trái gốc nhỏ hơn nút gốc.
- Với mọi cây con, tất cả các phần tử ở bên phải gốc lớn hơn nút gốc.

Nút con bên trái là gốc của cây con trái (left-subtree), nút con bên phải là gốc của cây con phải (right-subtree).

~~~[tree](itemShape=circle,itemSize=38,height=200,width=300)
(Root)
(L)|(R)
(LL)|(LR)|(RL)|(RR)
~~~

## Ứng dụng

Để quản lý 1 tập các phần tử có thứ tự (được sắp xếp theo thứ tự để tiện lợi trong thao tác thêm phần tử mới vào trong tập và lấy ra phần tử mong muốn), chúng ta có các kiểu dữ liệu cơ bản đó là:

- Mảng tuyến tính (array)
- Danh sách liên kết (linked list)

Cả 2 cách lưu trữ này đều có ưu điểm và nhược điểm riêng với các thao tác nhất định trong việc quản lý tập có thứ tự:

|                    | Ưu điểm                                   | Nhược điểm                                                                          |
|--------------------|-------------------------------------------|-------------------------------------------------------------------------------------|
| Mảng               | Tìm kiếm nhanh bằng binary search O(logn) | Thêm và xóa chậm do phải dịch chuyển toàn bộ các phần tử phía sau điểm cần sửa O(n) |
| Danh sách liên kết | Thêm hay xóa 1 phần tử tức thì O(1)       | Thao tác tìm kiếm chậm do phải duyệt qua từng phần tử từ đầu O(n)                   |

Từ đó cây nhị phân tìm kiếm được thiết kế kết hợp hài hòa ưu điểm của 2 kiểu dữ liệu trên để trở thành cấu trúc dữ liệu hiệu quả trong việc lưu trữ và truy xuất tập các phần tử có thứ tự. Các phần tử trong cây nhị phân tìm kiếm được sắp xếp theo thứ tự nhất định, các thao tác cơ bản như thêm, xóa và tìm kiếm đều có thể được thực hiện hiệu quả trong thời gian tính toán có độ phức tạp O(logn) trong đó n là số lượng các phần tử trong tập.

## Thao tacs

### Search

Để tìm kiếm 1 phần tử trong cây nhị phân tìm kiếm ta bắt đầu từ gốc của cây:

- 1. Nếu gốc là null dừng quá trình tìm kiếm trả về kết quả **không tìm thấy**.
- 2. So sánh với phần tử gốc nếu bằng trả về kết quả **tìm thấy tại vị trí này**.
- 3. Nếu phần tử cần tìm nhỏ hơn gốc ta lặp lại bước 1 với cây con bên trái (loại trừ gốc và cây con phải).
- 4. Nếu phần tử cần tìm lớn hơn gốc ta lặp lại bước 1 với cây con bên phải (loại trừ gốc và cây con trái).

Minh họa quá trình tìm kiếm số **6** trong cây nhị phân tìm kiếm như sau:

~~~[tree](itemShape=circle,itemSize=34,height=200,width=300)
(8)
(4)|(15)
(2)|(6)|(9)|(16)

(8):red
(4)|(15)
(2)|(6)|(9)|(16)

(8):grey
(4):red|(15):grey
(2)|(6)|(9):grey|(16):grey

(8):grey
(4):grey|(15):grey
(2):grey|(6):red|(9):grey|(16):grey
~~~

Dễ thấy với cây nhị phân tìm kiếm cân bằng như trên với mỗi phép so sánh ta loại trừ được 1 nửa số phần tử cần tìm.

### Insert

### Delete
