# Hàng đợi ưu tiên (Priority Queue)

Hàng đợi ưu tiên là 1 loại hàng đợi đặc biệt trong đó mỗi phần tử được gán với 1 giá trị ưu tiên. Các phần tử được lấy ra dựa trên thứ tự ưu tiên này, phần tử có giá trị ưu tiên lớn hơn sẽ được lấy ra trước.

Với các phần tử có giá trị ưu tiên giống nhau thứ tự lấy ra có thể dựa trên thứ tự của chúng trong hàng đợi (như trong hàng đợi thông thường First in first out - FIFO).

## Khác nhau giữa hàng đợi ưu tiên và hàng đợi thông thường

## Cài đặt hàng đợi ưu tiên

Hàng đợi ưu tiên có thể được cài đặt sử dụng mảng, danh sách liên kết, cấu trúc dữ liệu heap hoặc cây nhị phân tìm kiếm. Trong đó cấu trúc dữ liệu heap cho thấy được hiệu quả cao trong việc cài đặt hàng đợi ưu tiên. Trong bài viết này chúng ta sẽ đi vào chi tiết cách cài đặt sử dụng cấu trúc dữ liệu heap, mà cụ thể ở đây là dùng max heap.

So sánh các cách cài đặt hàng đợi ưu tiên

| Operations         | peek | insert   | delete   |
|--------------------|------|----------|----------|
| Linked List        | O(1) | O(n)     | O(1)     |
| Binary Heap        | O(1) | O(log n) | O(log n) |
| Binary Search Tree | O(1) | O(log n) | O(log n) |

## Cấu trúc dữ liệu heap

Cấu trúc dữ liệu heap là 1 cây nhị phân hoàn thiện (Complete Binary Tree) thỏa mãn tính chất:

- Max heap: với tất cả các nút key của nút cha lớn hơn hoặc bằng key của các nút con.
- Min heap: với tất cả các nút key của nút cha nhỏ hơn hoặc bằng key của các nút con.

Do heap là 1 cây nhị phân hoàn thiện chúng ta dùng mảng để biểu diễn heap. Hay nói cách khác chúng ta biểu diễn heap dùng mảng nhưng hình dung nó như 1 cây nhị phân hoàn thiện với tính chất:

- Phần tử ở vị trí i của mảng có nút con trái ở vị trí 2*i + 1, nút con phải ở vị trí 2*i + 2.
- Nút cha luôn luôn lớn hơn hoặc bằng các nút con của nó (với max-heap).

### Thao tác heapify (max-heap)

Thao tác heapify được thực hiện trên cây con của heap có gốc ở vị trí i:

- So sánh i và 2 phần tử ở vị trí 2*i + 1 (con trái), 2*i + 2 (con phải)
- Nếu phần tử i là lớn nhất trong 3 phần tử thao tác heapify dừng ở đây
- Nếu vị trí phần tử con trái hoặc phải là lớn nhất *largest* (2*i + 1 hoặc 2*i + 2)
  - Đổi chỗ 2 phần tử *largest* và *i*.
  - Lặp lại heapify với cây con có gốc ở vị trí *largest*.

### Thao tác biến 1 mảng thông thường thành heap

Để biến mảng thông thường thành heap ta thực hiện thao tác heapify cho tất cả các nút không phải là lá, theo thứ tự bắt đầu từ cuối trở về phần tử đầu tiên.

```
Lặp với i = n/2 - 1 cho đến 0
  heapify(i)
```

## Các thao tác trên hàng đợi ưu tiên

- Insert: thêm phần tử
- Delete: xóa phần tử
- Peek: trả về phần tử đầu tiên (có mức ưu tiên lớn nhất)
- Extract: trả về phần tử đầu tiên và xóa nó khỏi hàng đợi

### Insert

- Thêm phần tử mới vào cuối mảng
- Bắt đầu từ phần tử cha của phần tử mới, thực hiện thao tác heapify ngược lên trên

### Delete

### Peek

### Extract

## Ứng dụng của hàng đợi ưu tiên

## Minh họa

## Source code
