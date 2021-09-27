# BTree

B-Tree là cây tìm kiếm tự cân bằng và là dạng tổng quát của cây nhị phân tìm kiếm trong đó 1 node có thể có nhiều hơn 1 phần tử. Không như các cây tìm kiếm tự cân bằng khác như AVL tree hay Redblack tree được sử dụng chủ yếu trong bộ nhớ RAM, BTree được sinh ra do nhu cầu lưu trữ và tìm kiếm dữ liệu trên bộ nhớ cứng (hard disk). Với bộ nhớ cứng chi phí 1 lần truy cập dữ liệu là rất lớn và mỗi lần truy cập dữ liệu sẽ được đọc lên theo block nhiều bản ghi (1 bản ghi tương ứng 1 phần tử của nút) 1 lần nên Btree giúp giải quyết 2 vấn đề:

- Mỗi node có nhiều phần tử phù hợp với việc đọc dữ liệu trên đĩa cứng 1 lần theo block chứa nhiều bản ghi dữ liệu.
- Vì mỗi node có nhiều phần tử nên độ cao của cây giảm khiến cho số lần truy cập đĩa cứng khi thao tác cũng giảm.

Btree được tạo ra chủ yếu với mục đích để lưu trữ và tìm kiếm dữ liệu trên đĩa cứng (ví dụ *index* của database trên đĩa cứng là Btree). Tuy nhiên để đơn giản trong phạm vi bài viết này chúng ta chỉ **tập trung vào logic của Btree** được cài đặt trên bộ nhớ trong (RAM), nghĩa là các phần tử được lưu bằng biến số thay vì phải xử lý các bản ghi, con trỏ trên đĩa cứng đòi hỏi những kiến thức sâu hơn về cách ổ đĩa cứng lưu trữ và hoạt động.

Btree có nhiều biến thể trong đó phổ biến nhất là B+tree (Bplustree) tức là tất cả dữ liệu nằm ở nút lá, các nút phía trên chỉ đóng vai trò chứa khóa tìm kiếm để dẫn đến nút lá chứa dữ liệu mong muốn. Trong phạm vi bài viết này chúng ta sẽ nói về Btree cơ bản tức là nút lá và nút giữa đều chứa các phần tử có vai trò giống nhau.

## Tính chất Btree

Mỗi Btree phụ thuộc vào 1 giá trị ORDER là giá trị để xác định số lượng phần tử có trong 1 nút. Btree hợp lệ bảo đảm những tính chất sau:

- Số lượng nút con (cây con) của 1 nút không phải lá luôn nhiều hơn số phần tử của nút đó 1 đơn vị.
- Nút gốc có thể có ít nhất 1 (hoặc thậm chí là 0) phần tử. Các nút ngoài nút gốc có tối thiểu `MIN = ORDER / 2 - 1` phần tử (hay tối thiểu `ORDER / 2` nút con).
- Các nút có số phần tử tối đa `MAX = ORDER - 1` (hay tối đa `ORDER` nút con).
- Tất cả nút lá ở cùng độ sâu.
- Tất cả các phần tử (hay khóa tìm kiếm) của 1 nút được sắp xếp tăng dần, cây con nằm giữa 2 phần tử `k1` và `k2` chứa tất cả các phần tử nằm trong khoảng `k1 -> k2`.

Btree với **ORDER** 5 (số nút con lớn nhất là 5, số phần tử nhiều nhất 4, ít nhất là 2) có dạng như sau.

```[tree](shape=rect,size=30,height=200)
(3)
(1,2)(4,5,6,8)
```

Trên hình ta có khóa là các phần tử nằm trong hình vuông, số cây con (subtree) tương ứng với số đường link đi ra từ nút.

Độ phức tạp tính toán các thao tác `search, insert, delete` của Btree là O(logn) (cơ số logarit phụ thuộc vào giá trị ORDER).

## Cài đặt

Sử dụng 2 class cho nút **BTreeNode** và cho cây **Btree**, cấu trúc code có dạng như sau

```ts
export class BTreeNode {
  values: number[] = []
  children: BTreeNode[] = []

  // thêm
  insert(value: number): [number, BTreeNode] | null

  // xóa
  delete(value: number | null): number
}

export class BTree {
  root: BTreeNode | null = null
  order: number

  insert(value: number) {
    this.root.insert(value)
  }
  delete(value: number) {
    this.root.delete(value)
  }
}
```

## Thao tác thêm (Insert)

1. Phần tử mới luôn được thêm trực tiếp ở nút lá. Ta xuất phát từ gốc, dựa vào giá trị phần tử muốn thêm đi vào nhánh cây con phù hợp cho đến khi tìm được vị trí phù hợp ở 1 nút lá.

2. Thêm phần tử mới vào nút lá ở vị trí tìm được.

3. Lúc này ta kiểm tra số phần tử ở nút mới thêm nếu vượt quá số phần tử tối đa cho phép, thực hiện chia tách nút này thành 2 nút

    3.1. Chọn ra phần tử ở giữa nút gọi là phần tử chia.
    3.2. Các phần tử bên trái phần tử chia giữ nguyên vị trí, chuyển các phần tử bên phải phần tử chia sang 1 nút được tạo mới. Trong trường hợp là nút trong ta chia các phần tử con tương ứng.

4. Chuyển phần tử chia lên trên nút cha ở vị trí nút con đang xét. Thêm nút mới vào tập con của nút cha, ở vị trí bên cạnh nút hiện tại. Khi này số phần tử ở nút cha tăng thêm 1. Ta tiếp tục lặp lại bước 3 đối với nút cha trong trường hợp số phần tử của nút cha vượt quá số phần tử tối đa. Nếu không ta chuyển qua bước 5.

5. Trong trường hợp nút gốc bị chia, do không tồn tại nút cha ta tạo 1 nút mới bao gồm 1 phần tử chia và 2 con là nút gốc hiện tại và nút mới chia từ nút gốc. Sau đó cập nhật nút gốc là nút mới này.

Ta lấy **ORDER = 5** cho các ví dụ xuyên suốt bài viết.

- Số phần tử nhỏ nhất: 2
- Số phần tử lớn nhất: 4

```[tree](shape=rect,size=30,height=200)
(A,B,C,D)
(...)(E,G,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,B,C,D)
(...)(E,F{"c":"green"},G,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,B,C,D)
(...)(E,F,G,I{"c":"green"},H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,G{"c":"green"},B,C,D)
(...)(E,F)(I,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(B){"c":"green","t":"gốc mới"}
(A,G{"t":"gốc cũ"})(C,D)
(...)(E,F)(I,H)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```

### Cài đặt Insert

**1.** Hàm insert của nút

- Tham số truyền vào: số muốn thêm và số phần tử tối đa cho phép của 1 nút.
- Trả về
  - Giá trị `null` nếu sau khi thêm phần tử nút này không bị tách ra.
  - Mảng 2 giá trị`[phần tử chia, nút mới]` trong trường hợp nút bị tách sau khi thêm.
- Các bước
  - Tìm vị trí phù hợp.
  - Nếu là lá thêm phần tử vào vị trí tìm được.
  - Nếu là nút giữa:
    - Gọi đệ qui hàm `insert` đối với nút con ở vị trí tìm được.
    - Nếu nút con bị tách ra, thêm phần tử chia và nút mới tạo ra do chia tách vào nút hiện tại (nút cha của nút bị tách) vào vị trí tìm được ở bước trên.
  - Chia tách nút hiện tại nếu cần và trả về kết quả.

```ts
export class BTreeNode {
  // ...
  // insert and fix
  insert(value: number, max: number): [number, BTreeNode] | null {
    // tìm vị trí phần tử muốn thêm, biến found thể hiện có tìm thấy phần tử ở vị trí này không
    const [pos, found] = this.findPosition(value)

    // để đơn giản ta không xét trường thêm 2 phần tử giống nhau
    if (found) {
      return null
    }

    if (this.isleaf()) {
      // thêm phần tử vào nút lá
      this.values.splice(pos, 0, value)
    } else {
      // gọi đệ qui insert ở phần tử con
      const rs = this.children[pos].insert(value, max)
      if (rs) {
        // thêm phần tử vào nút giữa
        this.values.splice(pos, 0, rs[0])
        // thêm nút mới chia vào tập con
        this.children.splice(pos + 1, 0, rs[1])
      }
    }

    // kiểm tra nếu số phần tử vượt quá max thì cần chia tách
    return this.splitIfNeeded(max)
  }

  private splitIfNeeded(): [number, BTreeNode] | null {
    // kiểm tra số phần tử có vượt quá số tối đa không
    // nếu có chia tách nút, trả về phần tử chia và nút mới
    // nếu không trả về null
  }
  // ...
}
```

**2.** Hàm insert của cây

- Tham số truyền vào: số muốn thêm.
- Các bước:
  - Nếu không có nút gốc (cây rỗng), tạo nút gốc mới không có phần tử nào.
  - Gọi hàm insert đối với nút gốc.
  - Dựa vào kết quả gọi hàm, nếu nút gốc chia tách sau khi gọi tạo nút gốc mới với 1 phần tử là phần tử chia và 2 con là nút gốc hiện tại và nút mới chia.

```ts
export class BTree {
  root: BTreeNode | null = null

  constructor(public order: number) {}

  insert(value: number) {
    if (!this.root) {
      this.root = new BTreeNode()
    }
    const rs = this.root.insert(value, this.order - 1)
    if (rs) {
      const children = [this.root, rs[1]]
      this.root = new BTreeNode([rs[0]])
      this.root.children = children
    }
  }
}
```

## Thao tác xóa (Delete)

**1.** Ta tìm phần tử muốn xóa bằng cách bằng cách đi từ gốc dựa vào giá trị của phần tử đi theo các nhánh cây con phù hợp.

**2.** Nếu không tìm thấy phần tử muốn xóa thuật toán kết thúc.

**3.** Nếu tìm thấy phần tử muốn xóa ở nút lá

*3.1.* Xóa phần tử khỏi nút.

*3.2.* Nếu số phần tử của nút nhỏ hơn số phần tử tối thiểu cho phép (trong trường hợp không phải gốc), ta thực hiện tái cân bằng cây bằng 1 trong các cách sau

*3.2.1.* Xét nút anh em trái của nút hiện tại, nếu nút này tồn tại và có số phần tử lớn hơn số tối thiểu cho phép, ta thực hiện phép xoay phải để chuyển 1 phần tử từ nút trái sang nút hiện tại.

```[tree](shape=rect,size=30,height=200)
(H)
(A,E)(I,J)
(...)(B,C,D)(F,G{"c":"red"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,E{"c":"orange"})(I,J)
(...)(B,C,D{"c":"orange"})(F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,D{"c":"orange"})(I,J)
(...)(B,C)(E{"c":"orange"},F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```

*3.2.2.* Xét nút anh em phải của nút hiện tại, nếu nút này tồn tại và có số phần tử lớn hơn số tối thiểu cho phép, ta thực hiện phép xoay trái để chuyển 1 phần tử từ nút phải sang nút hiện tại.

```[tree](shape=rect,size=30,height=200)
(H)
(A,D)(I,J)
(...)(B,C{"c":"red"})(E,F,G)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,D{"c":"orange"})(I,J)
(...)(B)(E{"c":"orange"},F,G{"t":"anh em phải"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,E{"c":"orange"})(I,J)
(...)(B,D{"c":"orange"})(F,G)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```

*3.2.3* Nếu nút anh em trái và phải hoặc không tồn tại hoặc chỉ chứa số phần tử tối thiểu, thực hiện nhập (merge) nút hiện tại với nút anh em của nó (do đang xét trường hợp không phải gốc nên tồn tại ít nhất 1 nút anh em). Khi ấy số phần tử của nút tạo thành sẽ bằng tổng số phần tử của nút hiện tại cộng với số phần tử của nút anh em cộng thêm 1 phần tử kẹp giữa chuyển xuống từ nút cha, đồng thời số phần tử nút cha giảm đi 1. Tiếp tục kiểm tra nếu số phần tử ở nút cha nhỏ hơn số tối thiểu ta lặp lại thao tác 3.1 và 3.2 đối với phần tử cha.

Trường hợp xoay trái tiếp tục thực hiện ở nút cha (không phải lá) sau khi merge node lá.

```[tree](shape=rect,size=30,height=200)
(G)
(A,D)(H,I,J)
(...)(B,C{"c":"red"})(E,F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}(...){"id":"l4"}

(G)
(A,D{"c":"orange"})(H,I,J)
(...)(B)(E,F{"t":"anh em phải"}){"c":"orange"}(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}(...){"id":"l4"}

(G{"c":"orange"})
(A)(H{"c":"orange"},I,J)
(...)(B,D,E,F{"t":"merged node"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}(...){"id":"l4"}

(H)
(A,G{"c":"orange"})(I,J)
(...)(B,D,E,F{"t":"merged node"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}(...){"id":"l4"}
```

Trường hợp merge node tiếp tục xảy ra ở nút cha (không phải lá) sau khi merge node lá.

```[tree](shape=rect,size=30,height=200)
(G)
(A,D)(H,I)
(...)(B,C{"c":"red"})(E,F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(G)
(A,D{"c":"orange"})(H,I)
(...)(B)(E,F{"t":"anh em phải"}){"c":"orange"}(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(G{"c":"orange"})
(A)(H,I){"c":"orange"}
(...)(B,D,E,F{"t":"merged node"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,G,H,I{"t":"merged node"})
(...)(B,D,E,F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```

**4.** Trong trường hợp tìm thấy nút muốn xóa ở nút giữa (internal node) không phải là lá.

*4.1* Đi theo cây con bên trái của phần tử vừa tìm được.

*4.2* Tìm phần tử lớn nhất của cây con này bằng cách đi về phía tận cùng phải của cây, cho đến khi tìm được phần tử lớn nhất này là phần tử cuối cùng của nút lá tận cùng phải gọi là phần tử thay thế.

*4.3* Gán phần tử thay thế vừa tìm được đè lên vị trí của phần tử muốn xóa.

*4.4* Thực hiện xóa phần tử thay thế ở vị trí nút lá tìm được ở bước *4.2*, bài toán trở về bước **3**.

```[tree](shape=rect,size=30,height=200)
(G)
(A,D)(H,I)
(...)(B,C)(E,F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(G){"c":"red","t":"xóa"}
(A,D)(H,I)
(...)(B,C)(E,F{"c":"orange","t":"thay thế"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(F)
(A,D{"c":"orange"})(H,I)
(...)(B,C)(E){"c":"orange"}(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(F){"c":"orange"}
(A)(H,I){"c":"orange"}
(...)(B,C,D,E)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(A,F,H,I)
(...)(B,C,D,E)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```

### Cài đặt Delete

**1.** Hàm delete của nút

- Bắt đầu từ gốc tìm vị trí nhánh cây phù hợp với phần tử muốn xóa.
- Nếu là nút lá:
  - Nếu tìm thấy, xóa phần tử và trả về phần tử này.
  - Nếu không tìm thấy trả về `null`
- Nếu là nút giữa:
  - Nếu chưa tìm thấy, gọi đệ qui xóa đối với nút con ở vị trí tìm được.
  - Nếu tìm thấy:
    - Gọi đệ qui xóa phần tử lớn nhất (phần tử thay thế) đối với nút con tương ứng.
    - Ghi đè phần tử thay thế vào vị trí phần tử muốn xóa.
  - Sau khi gọi xóa đối với nút con, kiểm tra số phần tử của nút con này nếu nhỏ hơn số tối thiểu cho phép ta thực hiện tái cân bằng cây: xoay trái, xoay phải hoặc hợp nhất nút. Trong trường hợp hợp nhất nút số phần tử của nút cha (nút hiện tại) sẽ giảm đi 1, như vậy sau khi rút đệ qui lên trên ta tiếp tục kiểm tra và tái cân bằng cây nếu cần.

```ts
export class BtreeNode {
  // ...
  delete(value: number, min: number): number | null {
    const [pos, found] = this.findPosition(value)
    let out: number | null = null

    // leaf node
    if (this.isleaf()) {
      if (found) {
        // remove
        out = this.values.splice(pos, 1)[0]
      }
    } else {
      // internal node
      const child = this.children[pos]

      if (found) {
        // delete the max value of the child
        const out = child.deleteMax(min, onChange)
        if (out) {
          // replace the found position with the deleted value
          this.values[pos] = out
        }
      } else {
        out = child.delete(value, min, onChange)
      }

      // after delete, grow child if needed
      if (child.values.length < min) {
        this.growChild(pos, min)
        // incase empty root, we skip this change
        if (this.values.length) onChange()
      }
    }

    return out
  }

  private deleteMax(min: number): number | null {
    let out: number | null = null
    if (this.isleaf()) {
      out = this.values.pop() || null
    } else {
      const pos = this.values.length
      const child = this.children[pos]
      out = child.deleteMax(min, onChange)
  
      if (child.values.length < min) {
        this.growChild(pos, min)
      }
    }

    return out
  }

  private growChild(i: number, min: number) {
    // đối với nút con child = this.children[i],
    // xoay trái
    // xoay phải
    // merge node
  }
  // ...
}
```

**2.** Hàm delete của cây

- Gọi hàm xóa đối với nút gốc (nếu tồn tại).
- Trong trường hợp số phần tử của nút gốc trở về 0 (số con là 1) loại bo nút gốc này và cập nhật nút gốc mới là nút con của nó.

```ts
export class BTree {
  root: BTreeNode | null = null

  constructor(public order: number) {}

  delete(value: number) {
    if (!this.root) {
      return
    }

    this.root.delete(value, Math.ceil(this.order / 2) - 1, this.onChange)

    // update new root
    if (this.root.values.length === 0) {
      this.root = this.root.children[0] || null
    }
  }
}
```

## Mã nguồn

Full Typescript source

```ts
export class BTreeNode {
  children: BTreeNode[] = []
  next: BTreeNode | null = null

  constructor(public values: number[] = []) {}

  isleaf(): Boolean {
    return this.children.length === 0
  }

  search(value: number): [BTreeNode, number] | null {
    return null
  }

  // insert and fix
  insert(value: number, max: number): [number, BTreeNode] | null {
    const [pos, found] = this.findPosition(value)

    // no duplicate
    if (found) {
      return null
    }

    if (this.isleaf()) {
      this.values.splice(pos, 0, value)
    } else {
      const rs = this.children[pos].insert(value, max)
      if (rs) {
        this.values.splice(pos, 0, rs[0])
        this.children.splice(pos + 1, 0, rs[1])
      }
    }

    return this.splitIfNeeded(max)
  }

  private split(i: number): [number, BTreeNode] {
    // new right node
    const right = new BTreeNode()
    right.values = this.values.splice(i + 1)
    // remove the upper item
    const item = this.values.pop()!

    if (!this.isleaf()) {
      right.children = this.children.splice(i + 1)
    }

    right.next = this.next
    this.next = right

    return [item, right]
  }

  private splitIfNeeded(max: number): [number, BTreeNode] | null {
    if (this.values.length <= max) {
      return null
    }

    return this.split(Math.floor(max / 2))
  }

  // delete
  delete(value: number, min: number, onChange = () => {}): number | null {
    const [pos, found] = this.findPosition(value)
    let out: number | null = null

    // leaf node
    if (this.isleaf()) {
      if (found) {
        // remove
        out = this.values.splice(pos, 1)[0]
        onChange()
      }
    } else {
      // internal node
      const child = this.children[pos]

      if (found) {
        // delete the max value of the child
        const out = child.deleteMax(min, onChange)
        if (out) {
          // replace the found position with the deleted value
          this.values[pos] = out
          onChange()
        }
      } else {
        out = child.delete(value, min, onChange)
      }

      // after delete, grow child if needed
      if (child.values.length < min) {
        this.growChild(pos, min)
        // incase empty root, we skip this change
        if (this.values.length) onChange()
      }
    }

    return out
  }

  // delete last value of the right-most child
  private deleteMax(min: number, onChange = () => {}): number | null {
    let out: number | null = null
    if (this.isleaf()) {
      out = this.values.pop() || null
      onChange()
    } else {
      const pos = this.values.length
      const child = this.children[pos]
      out = child.deleteMax(min, onChange)
  
      if (child.values.length < min) {
        this.growChild(pos, min)
        onChange()
      }
    }

    return out
  }

  private growChild(i: number, min: number) {
    if (i > 0 && this.children[i-1].values.length > min) {
      // steal from left child
      const child = this.children[i]
      const stealFrom = this.children[i-1]
      const stolenItem = stealFrom.values.pop()!

      // push upper item to the child's head
      child.values.splice(0, 0, this.values[i-1])
      // move stolenItem up
      this.values[i-1] = stolenItem
      if (!child.isleaf()) {
        // move last child of stealFrom to child's head
        child.children.splice(0, 0, stealFrom.children.pop()!)
      }
    } else if (i < this.values.length && this.children[i+1].values.length > min) {
      // steal from right child
      const child = this.children[i]
      const stealFrom = this.children[i+1]
      const stolenItem = stealFrom.values.shift()!

      child.values.push(this.values[i])
      if (!child.isleaf()) {
        child.children.push(stealFrom.children.shift()!)
      }
      this.values[i] = stolenItem
    } else {
      // merge with right child
      if (i >= this.values.length) {
        i--
      }

      const child = this.children[i]
      const mergeItem = this.values.splice(i, 1)[0]
      const mergeChild = this.children.splice(i + 1, 1)[0]
      child.children.push(...mergeChild.children)
      child.values.push(mergeItem, ...mergeChild.values)
      child.next = mergeChild.next
      // TODO free node mergeChild?
    }
  }

  private findPosition(value: number): [number, boolean] {
    let i = 0
    for (; i < this.values.length && value > this.values[i]; i++) {}

    return [i, value === this.values[i]]
  }
}

export class BTree {
  root: BTreeNode | null = null

  constructor(public order: number) {}

  insert(value: number) {
    if (!this.root) {
      this.root = new BTreeNode()
    }
    const rs = this.root.insert(value, this.order - 1)
    if (rs) {
      const children = [this.root, rs[1]]
      this.root = new BTreeNode([rs[0]])
      this.root.children = children
    }
  }

  delete(value: number) {
    if (!this.root) {
      return
    }

    const out = this.root.delete(value, Math.ceil(this.order / 2) - 1)

    // update new root
    if (this.root.values.length === 0) {
      this.root = this.root.children[0] || null
    }
  }
}
```

## Minh họa

```[btree](shape=rect,size=32,height=250)
31 30 23 50 45 48 70 67 75
```
