# Hàng đợi ưu tiên (Priority Queue)

Với hàng đợi thông thường các phần tử được thêm vào và lấy ra theo qui tắc vào trước ra trước FIFO (First In First Out). Hàng đợi ưu tiên là 1 loại hàng đợi đặc biệt trong đó mỗi phần tử được gán với 1 giá trị ưu tiên. Các phần tử được lấy ra dựa trên thứ tự ưu tiên này, phần tử có giá trị ưu tiên lớn hơn sẽ được lấy ra trước.

Với các phần tử có giá trị ưu tiên giống nhau thứ tự lấy ra có thể dựa trên thứ tự của chúng trong hàng đợi (như trong hàng đợi thông thường First in first out - FIFO) hoặc không tùy theo cách cài đặt.

## Các thao tác trên hàng đợi ưu tiên

Giống như hàng đợi thông thường chúng ta có các thao tác cơ bản sau

- **Push**: thêm phần tử.
- **Top**: trả về phần tử đầu tiên (có mức ưu tiên lớn nhất).
- **Pop**: trả về phần tử đầu tiên và xóa nó khỏi hàng đợi.

## Các cách cài đặt hàng đợi ưu tiên

Hàng đợi ưu tiên có thể được cài đặt sử dụng mảng, danh sách liên kết, cấu trúc dữ liệu heap hoặc cây nhị phân tìm kiếm.

So sánh các cách cài đặt hàng đợi ưu tiên:

| Operations         | Push     | Top    | Push     |
|--------------------|----------|--------|----------|
| Mảng               | O(1)     | O(1)   | O(n)     |
| Danh sách liên kết | O(n)     | O(1)   | O(1)     |
| Heap               | O(log n) | O(1)   | O(log n) |

### Dùng mảng không thứ tự

Cách cài đặt đơn sơ nhất (naive solution) là sử dụng mảng không thứ tự (unordered list).

- **Push** Thao tác được thực hiện bằng cách thêm phần tử vào cuối mảng, độ phức tạp O(1).
- **Top** Duyệt qua tất cả các phần tử để tìm và trả về phần tử có độ ưu tiên lớn nhất, độ phức tạp O(n).
- **Pop** Thực hiện duyệt tìm vị trí phần tử có độ ưu tiên lớn nhất, sau đó xóa phần tử bằng cách dịch tất cả các phần tử sau vị trí này lên trước 1 vị trí, độ phức tạp O(n).

### Dùng danh sách liên kết có thứ tự

Có thể cải tiến cách cài đặt trên bằng cách sử dụng danh sách liên kết được sắp xếp theo thứ tự mức ưu tiên cao đến thấp.

- **Push** Duyệt danh sách liên kết (đẵ được sắp xếp) tìm ra vị trí thêm phần tử mới, độ phức tạp O(n).
- **Top** Trả về phần tử đầu tiên, độ phức tạp O(1).
- **Pop** Trả về và xóa phần tử đầu tiên của danh sách liên kết, độ phức tạp O(1).

### Dùng cấu trúc dữ liệu heap

Cấu trúc dữ liệu heap là cấu trúc dữ liệu sử dụng mảng biểu diễn 1 cây nhị phân hoàn thiện (Complete Binary Tree). Ở đây ta dùng max-heap cho hàng đợi ưu tiên.

- **Push** Thêm phần tử mới vào max-heap, độ phức tạp O(log n).
- **Top** Trả về phần tử đầu tiên, độ phức tạp O(1).
- **Pop** Trả về và xóa phần tử đầu tiên của heap, độ phức tạp O(log n).

Phần tiếp theo của bài viết sẽ giải thích kỹ hơn về cách hoạt động của cấu trúc dữ liệu heap.

## Cấu trúc dữ liệu heap (max-heap)

Cấu trúc dữ liệu heap là 1 cây nhị phân hoàn thiện (Complete Binary Tree) thỏa mãn tính chất: với tất cả các nút key của nút cha lớn hơn hoặc bằng key của các nút con (max-heap).

Do heap là 1 cây nhị phân hoàn thiện chúng ta dùng mảng để biểu diễn heap. Hay nói cách khác chúng ta biểu diễn heap dùng mảng nhưng hình dung nó như 1 cây nhị phân hoàn thiện với tính chất:

- Phần tử ở vị trí i của mảng có nút con trái ở vị trí 2*i + 1, nút con phải ở vị trí 2*i + 2.
- Nút cha luôn luôn lớn hơn hoặc bằng các nút con của nó (max-heap).

### Thao tác heapify (max-heap)

Thao tác heapify được thực hiện trên cây con của heap có gốc ở vị trí i:

- So sánh i và 2 phần tử ở vị trí 2*i + 1 (con trái), 2*i + 2 (con phải).
- Nếu phần tử i là lớn nhất trong 3 phần tử thao tác heapify dừng ở đây.
- Nếu vị trí phần tử con trái hoặc phải là lớn nhất *largest* (2*i + 1 hoặc 2*i + 2).
  - Đổi chỗ 2 phần tử *largest* và *i*.
  - Lặp lại heapify với cây con có gốc ở vị trí *largest*.

```[tree](size=34,height=200)
(2){"c": "red", "t": "heapify vị trí này"}
(4)(7)
(1)(3)(5)()

(2){"c": "red", "t": "heapify vị trí này"}
(4)(7){"c": "green", "t": "lớn nhất trong 3"}
(1)(3)(5)()

(7)
(4)(2){"c": "red", "t": "heapify vị trí này"}
(1)(3)(5)()

(7)
(4)(2){"c": "red", "t": "heapify vị trí này"}
(1)(3)(5){"c": "green", "t": "lớn hơn cha"}()

(7)
(4)(5)
(1)(3)(2){"c": "red"}()
```

Typescript

```ts
function heapify(i: number): void {
  const l = 2 * i + 1
  const r = 2 * i + 2
  let largest = i
  const size = this.items.length
  if (l < size && this.comparator(this.items[l], this.items[i]) < 0) {
    // item l > item i
    largest = l
  }
  if (r < size && this.comparator(this.items[r], this.items[largest]) < 0) {
    // item r > item largest
    largest = r
  }
  if (largest !== i) {
    // move down to children
    this.swap(i, largest)
    this.heapify(largest)
  }
}
```

### Thao tác biến 1 mảng thông thường thành heap

Để biến mảng thông thường thành heap ta thực hiện thao tác heapify cho tất cả các nút không phải là lá, theo thứ tự bắt đầu từ cuối trở về phần tử đầu tiên.

```psuedo
Lặp với i = n/2 - 1 cho đến 0
  heapify(i)
```

## Cài đặt hàng đợi ưu tiên dùng max-heap

- *Push*: thêm phần tử
- *Top*: trả về phần tử đầu tiên (có mức ưu tiên lớn nhất)
- *Pop*: trả về phần tử đầu tiên và xóa nó khỏi hàng đợi

### Push

- Thêm phần tử mới vào cuối mảng
- Bắt đầu từ phần tử cha của phần tử mới, thực hiện thao tác heapify ngược lên trên (Chú ý thao tác này ngược với thao tác heapify được nhắc ở trên là đi theo hướng xuống dưới cây).

```[tree](size=34,height=200)
(9)
(4)(5)
(1)(3)(2)()

(9)
(4)(5)
(1)(3)(2)(7){"c":"green"}

(9)
(4)(7){"c":"green"}
(1)(3)(2)(5)
```

Typescript

```ts
function push(item: T): void {
  this.items.push(item)
  for (let i = this.items.length - 1; i > 0;) {
    const parent = Math.floor((i - 1) / 2)
    // item i > item parent
    if (this.comparator(this.items[i], this.items[parent]) < 0) {
      this.swap(i, parent)
      i = parent
    } else {
      break
    }
  }
}
```

### Top

Đơn giản trả về phần tử đầu tiên của mảng.

### Pop

- Lấy ra phần tử đầu tiên để trả về.
- Gán phần tử cuối cùng của Heap đè lên phần tử đầu tiên.
- Thực hiện thao tác heapfify với phần tử đầu tiên.

```[tree](size=34,height=200)
(9)
(4)(7)
(1)(3)(5)(2)

(2){"c": "red"}
(4)(7)
(1)(3)(5)()

(7)
(4)(2){"c": "red"}
(1)(3)(5)()

(7)
(4)(5)
(1)(3)(2){"c": "red"}()
```

Typescript

```ts
function pop(): T | null {
  if (this.isEmpty()) {
    return null
  }

  const item = this.items[0]
  const last = this.items.pop()!
  
  if (!this.isEmpty()) {
    this.items[0] = last
    this.heapify(0)
  }

  return item
}
```

## Ứng dụng của hàng đợi ưu tiên

- Lập lịch thực thi task cho CPU.

- Thuật toán đồ thị như:
  - Dijkstra tìm đường đi ngắn nhất giữa 2 điểm.
  - Prim tìm cây khung nhỏ nhất

## Minh họa

Chú ý không sử dụng nhãn trùng lặp cho các nút. Số hiển thị trong nút là mức độ ưu tiên, chữ bên cạnh trong ngoặc vuông là chỉ số index trong mảng, tiếp đến là label để phân biệt các nút.

```[pqvisual](size=32,height=250)
A 9, B 3, C 5, D 1, E 4, F 2
```

## Source code

Code Typescript đầy đủ cho hàng đợi ưu tiên. Chú ý ở đây ta dùng kiểu tổng quát cho các phần tử và cần thêm 1 hàm truyền vào từ bên ngoài để so sánh mức độ ưu tiên giữa các phần tử này.

```ts
export type Comparator<T> = (t1: T, t2: T) => -1 | 0 | 1

const DEFAULT_COMPARATOR: Comparator<any> = (a, b) => {
  return a < b ? -1 : a === b ? 0 : 1
}

export class PriorityQueue<T> {
  private items: T[] = []

  constructor(private readonly comparator: Comparator<T> = DEFAULT_COMPARATOR) {}

  isEmpty(): boolean {
    return this.items.length === 0
  }

  push(item: T): void {
    this.items.push(item)
    for (let i = this.items.length - 1; i > 0;) {
      const parent = Math.floor((i - 1) / 2)
      if (this.comparator(this.items[i], this.items[parent]) < 0) {
        this.swap(i, parent)
        i = parent
      } else {
        break
      }
    }
  }

  pop(): T | null {
    if (this.isEmpty()) {
      return null
    }

    const item = this.items[0]
    const last = this.items.pop()!
    
    if (!this.isEmpty()) {
      this.items[0] = last
      this.heapify(0)
    }

    return item
  }

  top(): T | null {
    if (this.isEmpty()) {
      return null
    }

    return this.items[0]
  }

  private heapify(i: number): void {
    const l = 2 * i + 1
    const r = 2 * i + 2
    let largest = i
    const size = this.items.length
    if (l < size && this.comparator(this.items[l], this.items[i]) < 0) {
      largest = l
    }
    if (r < size && this.comparator(this.items[r], this.items[largest]) < 0) {
      largest = r
    }
    if (largest !== i) {
      this.swap(i, largest)
      this.heapify(largest)
    }
  }

  private swap(i: number, j: number): void {
    const tmp = this.items[i]
    this.items[i] = this.items[j]
    this.items[j] = tmp
  }
}
```

## Bài viết liên quan

```[articlecards]()
iHQRk7ODINxd2AZ4Dbcxa
0hfMzozRhMbAQkr4AzZJ
```
