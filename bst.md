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

```[tree](size=34,height=250)
(50)
(30)(70)
(23)(35)()(80)
(11){"t":"Min","c":"red"}(25)(31)(42)(73){"p":"80"}(85){"p":"80","c":"red","t":"Max"}
```

### Tìm phần tử sau (Successor) và trước (Predecessor)

Nút trước và nút sau trong cây nhị phân tìm kiếm được xác định bởi thứ tự các nút được sắp xếp tăng dần (theo trình tự duyệt giữa). Ví dụ ta có nút x trong cây nhị phân tìm kiếm, các bước để tìm nút sau và nút trước như sau

- Nút sau (Successor)
  - Nếu cây con phải của nút x là khác rỗng, ta có nút sau của x là nút tận cùng trái của cây con phải của x.
  - Nếu cây con phải của nút x là rỗng và nếu tồn tại nút sau của x là y, thì y là nút tổ tiên (ancestor) của x gần nhất thỏa mãn: nút con trái của y cũng là tổ tiên của x.

- Nút trước (Predecessor)
  - Nếu cây con trái của nút x là khác rỗng, ta có nút trước của x là nút tận cùng phải của cây con trái của x.
  - Nếu cây con trái của nút x là rỗng và nếu tồn tại nút trước của x là y, thì y là nút tổ tiên (ancestor) của x gần nhất thỏa mãn: nút con phải của y cũng là tổ tiên của x.

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

Để xóa 1 nút trên cây nhị phân tìm kiếm ta cần đảm bảo các nút còn lại vẫn thỏa mãn tính chất của cây nhị phân tìm kiếm: tất cả các nút thuộc cây con trái <= nút gốc <= tất cả các nút thuộc cây con phải. Ví dụ x là nút cần xóa, ta xét 3 trường hợp như sau:

- Nếu x là nút lá ta đơn giản loại bỏ nút x khỏi cây, các phần tử còn lại giũ nguyên.
- Nếu x chỉ có 1 nút con (trái hoặc phải), ta xóa nút x và thay thế x bằng nút con của nó, các phần tử còn lại giữ nguyên.
- Nếu x có cả nút con trái và nút con phải:
  - Ghi đè phần tử nhỏ nhất y thuộc cây con phải của x (y là nút tận cùng trái thuộc cây con phải của x) vào vị trí của x.
  - Xóa y, vì y là nút tận cùng trái nên y ko có nút con trái, bài toán trở về 1 trong 2 trường hợp đơn giản ở trên.

Ví dụ trường hợp 3, xóa nút gốc **36** trong cây nhị phân tìm kiếm sau

```[tree](size=34,height=250)
(36){"c":"red","t":"xóa nút này"}
(30)(50)
(23)(35)(45)(70)
(){"p":"45"}(48){"p":"45"}(67){"p":"70"}(75){"p":"70"}

(36){"c":"red","t":"xóa nút này"}
(30)(50)
(23)(35)(45){"c":"green","t":"nút kế tiếp"}(70)
(){"p":"45"}(48){"p":"45"}(67){"p":"70"}(75){"p":"70"}

(45){"c":"green","t":"ghi đè lên nút cần xóa"}
(30)(50)
(23)(35)(45){"c":"red","t":"xóa nút này","id":"d"}(70)
(){"p":"d"}(48){"p":"d"}(67){"p":"70"}(75){"p":"70"}

(45)
(30)(50)
(23)(35)(45){"c":"red","t":"xóa nút này","id":"d"}(70)
(){"p":"d"}(48){"p":"d","t":"nút con duy nhất"}(67){"p":"70"}(75){"p":"70"}

(45)
(30)(50)
(23)(35)(48)(70)
(67){"p":"70"}(75){"p":"70"}
```

## Minh hoạ trực quan

Các bạn nhập input cho các thao tác Insert, Delete để xem cách thuật toán hoạt động theo từng bước

```[bstvisual](size=32,height=250)
31 30 23 50 45 48 70 67 75
```

## Mã nguồn

Typescript

```ts
export class BinaryNode {
  left: BinaryNode | null = null
  right: BinaryNode | null = null

  constructor(public data: number) {}

  search(data: number): BinaryNode | null {
    if (data === this.data) {
      return this
    }

    const next = data < this.data ? this.left : this.right

    return next === null ? null : next.search(data)
  }

  insert(data: number): boolean {
    if (data === this.data) {
      return false
    }

    const isGoLeft = data < this.data

    const next = isGoLeft ? this.left : this.right

    if (next === null) {
      const newNode = new BinaryNode(data)
      if (isGoLeft) {
        this.left = newNode
      } else {
        this.right = newNode
      }

      return true
    }

    return next.insert(data)
  }

  delete(data: number): BinaryNode | null {
    if (data < this.data) {
      if (this.left) this.left = this.left.delete(data)
    } else if (data > this.data) {
      if (this.right) this.right = this.right.delete(data)
    } else {
      if (this.left === null) {
        return this.right
      }

      if (this.right === null) {
        return this.left
      }

      // replace with min value of right subtree
      const nextVal = this.right.minValue()
      this.data = nextVal

      // delete min value of right subtree
      this.right = this.right.delete(nextVal)
    }

    return this
  }

  traverseInOrder(cb: (data: number) => void): void {
    if (this.left) {
      this.left.traverseInOrder(cb)
    }

    cb(this.data)

    if (this.right) {
      this.right.traverseInOrder(cb)
    }
  }

  traversePreOrder(cb: (data: number) => void): void {
    cb(this.data)

    if (this.left) {
      this.left.traversePreOrder(cb)
    }

    if (this.right) {
      this.right.traversePreOrder(cb)
    }
  }

  traversePostOrder(cb: (data: number) => void): void {
    if (this.left) {
      this.left.traversePostOrder(cb)
    }

    if (this.right) {
      this.right.traversePostOrder(cb)
    }

    cb(this.data)
  }

  minValue(): number {
    let node = this as BinaryNode
    while (node.left) node = node.left

    return node.data
  }
}

export class BinaryTree {
  root: BinaryNode | null = null

  search(data: number): BinaryNode | null {
    return this.root ? this.root.search(data): null
  }

  insert(data: number): boolean {
    if (!this.root) {
      this.root = new BinaryNode(data)
      return true
    }

    return this.root.insert(data)
  }

  delete(data: number): void {
    if (!this.root) {
      return
    }

    this.root = this.root.delete(data)
  }
}
```
