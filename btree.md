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

## Cài đặt

Sử dụng 2 class cho nút **BTreeNode** và cho cây **Btree**, cấu trúc code có dạng như sau

```ts
export class BTreeNode {
    values: number[] = []
    children: BTreeNode[] = []

    // thêm
    insert(value: number): [number, BTreeNode] | null

    // xóa
    delete(value: number | null, replace?: [BTreeNode, number]): number
}

export class BTree {
    root: BTreeNode | null = null

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

Với **order** 5 ta có

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
  // ...
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

  constructor(public minOrder: number) {}

  insert(value: number) {
    if (!this.root) {
      this.root = new BTreeNode()
    }
    const rs = this.root.insert(value, this.minOrder * 2 - 1)
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
(...)(B,C,D)(F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,D)(I,J)
(...)(B,C)(E{"c":"orange"},F)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}
```

*3.2.2.* Xét nút anh em phải của nút hiện tại, nếu nút này tồn tại và có số phần tử lớn hơn số tối thiểu cho phép, ta thực hiện phép xoay trái để chuyển 1 phần tử từ nút phải sang nút hiện tại.

```[tree](shape=rect,size=30,height=200)
(H)
(A,D)(I,J)
(...)(B,C{"c":"red"})(E,F,G)(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,D{"c":"orange"})(I,J)
(...)(B)(E,F,G{"t":"anh em phải"})(...){"id":"l1"}(...){"id":"l2"}(...){"id":"l3"}

(H)
(A,E)(I,J)
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

## Mã nguồn

## Minh họa

```[btree](shape=rect,size=32,height=250)
31 30 23 50 45 48 70 67 75
```
