# Knuth Morris Pratt

Thuật toán Knuth Morris Pratt (KMP) là thuật toán hiệu quả tìm chuỗi con (chuỗi mẫu - pattern) bên trong chuỗi cha.

## Bài toán

Cho 1 chuỗi mẫu pattern P độ dài m và đoạn text T độ dài n, tìm tất cả các lần mà chuỗi P xuất hiện bên trong đoạn text T. Ta có thể coi n > m.

## Cách giải đơn thuần (naive solution)

Dùng 2 vòng lặp

- Thử lần lượt từng vị trí trên T (n - m + 1 vị trí [0...n - m])
- Với từng vị trí trên T, thử khớp P bắt đầu từ vị trí này (m kí tự), cho đến khi khớp hoàn toàn hoặc gặp kí tự sai khác.

```[visual](offsetX=10,shape=rect,autoId=1,size=34)
linear(offsetY=10)
A{"t":"start"} A A A A A A A A A
linear(offsetY=70,id=2)
A A A A B

linear(offsetY=10)
A{"t":"start","c":"green"} A A A A A A A A A
linear(offsetY=70,id=2)
A{"c":"green","t":"^"} A A A A

linear(offsetY=10)
A{"t":"start","c":"green"} A{"c":"green"} A A A A A A A A
linear(offsetY=70,id=2)
A{"c":"green"} A{"c":"green","t":"^"} A A B

linear(offsetY=10)
A{"t":"start","c":"green"} A{"c":"green"} A{"c":"green"} A A A A A A A
linear(offsetY=70,id=2)
A{"c":"green"} A{"c":"green"} A{"c":"green","t":"^"} A B

linear(offsetY=10)
A{"t":"start","c":"green"} A{"c":"green"} A{"c":"green"} A{"c":"green"} A A A A A A
linear(offsetY=70,id=2)
A{"c":"green"} A{"c":"green"} A{"c":"green"} A{"c":"green","t":"^"} B

linear(offsetY=10)
A{"t":"start","c":"green"} A{"c":"green"} A{"c":"green"} A{"c":"green"} A{"c":"red"} A A A A A
linear(offsetY=70,id=2)
A{"c":"green"} A{"c":"green"} A{"c":"green"} A{"c":"green"} B{"c":"red","t":"^"}

linear(offsetY=10)
A A{"t":"start"} A A A A A A A A
linear(offsetY=70,id=2)
 A{"id":"0"} A{"id":"1"} A{"id":"2"} A{"id":"3"} B{"id":"4"}

linear(offsetY=10)
A A{"t":"start","c":"green"} A A A A A A A A
linear(offsetY=70,id=2)
 A{"id":"0","c":"green"} A{"id":"1"} A{"id":"2"} A{"id":"3"} B{"id":"4"}
```

Ta thấy sau khi kết thúc so sánh chuỗi mẫu ở vị trí đầu tiên của đoạn text, dịch chuyển sang vị trí thứ 2 ta so sánh lại từ đầu chuỗi mẫu. Trong khi ta đã biết 4 kí tự đầu của đoạn text chính là 4 kí tự đầu của chuỗi mẫu, ta tìm cách cải tiến thuật toán bằng cách tận dụng thông tin đã biết từ lần so sánh trước.

sau khi dịch lên 1 vị trí ta biết 3 kí tự đầu sẽ khớp và chỉ cần 

## Cải tiến bằng thuật toán KMP

Giả sử ta có chuỗi mẫu ABABAC và đã khớp được 5 kí tự đầu ở 1 vị trí trong đoạn text

```[visual](offsetX=10,shape=rect,autoId=1,size=34)
linear(offsetY=10)
... A{"t":"start","c":"green"} B{"c":"green"} A{"c":"green"} B{"c":"green"} A{"c":"green"} ...
linear(offsetY=70,id=2)
 A{"c":"green"} B{"c":"green"} A{"c":"green"} B{"c":"green"} A{"c":"green"} C{"c":"red","t":"^"}
```

Với thuật toán đơn sơ ta sẽ dịch chuỗi mẫu lên 1 vị trí và bắt đầu so sánh lại từ đầu từng kí tự. Tuy nhiên ta thấy giả sử sau khi đã khớp được 5 kí tự `A1 A2 A3 A4 A5`, để dịch lên 1 vị trí mà vẫn có khả năng khớp ta phải có `A1 A2 A3 A4` = `A2 A3 A4 A5`, hoặc để dịch lên 2 vị trí mà vẫn có khả năng khớp ta phải có `A1 A2 A3` = `A3 A4 A5`, với 5 kí tự này thuộc chuỗi mẫu đã biết trước.

```[visual](offsetX=10,shape=rect,autoId=1,size=34)
linear(offsetY=10)
... A1{"t":"start","c":"green"} A2{"c":"green"} A3{"c":"green"} A4{"c":"green"} A5{"c":"green"} ...
linear(offsetY=70,id=2)
 A1{"c":"green"} A2{"c":"green"} A3{"c":"green"} A4{"c":"green"} A5{"c":"green"} A6

linear(offsetY=10)
... A1 A2{"t":"start","c":"green"} A3{"c":"green"} A4{"c":"green"} A5{"c":"green"} ...
linear(offsetY=70,id=2)
  A1{"c":"green","id":"1"} A2{"c":"green","id":"2"} A3{"c":"green","id":"3"} A4{"c":"green","id":"4"} A5{"id":"5"} A6{"id":"6"}

linear(offsetY=10)
... A1 A2 A3{"t":"start","c":"green"} A4{"c":"green"} A5{"c":"green"} ...
linear(offsetY=70,id=2)
   A1{"c":"green","id":"1"} A2{"c":"green","id":"2"} A3{"c":"green","id":"3"} A4{"id":"4"} A5{"id":"5"} A6{"id":"6"}
```

Trong trường hợp `A1 A2 A3` = `A3 A4 A5`, ta gọi chuỗi `A1 A2 A3` (hay `A3 A4 A5`) là prefix-suffix của `A1 A2 A3 A4 A5` tức là chuỗi `A1 A2 A3` vừa là tiền tố vừa là hậu tố của `A1 A2 A3 A4 A5`. Gọi độ dài đoạn tiền tố hậu tố này là x, nếu vị trí hiện tại trên đoạn text là K thì vị trí tiếp theo để thử khớp sẽ là `K + (5 - x)` (5 là độ dài của đoạn đã khớp ở bước hiện tại `A1 A2 A3 A4 A5`). Ta thấy cần x là dài nhất có thể vì nếu không ta sẽ bỏ qua vị trí có khả năng khớp tiếp theo, và `A1 A2 A3 A4 A5` chính là chuỗi tiền tố hậu tố của chính nó nhưng ta cần tìm chuỗi nhỏ hơn để có thể đi tiếp.

Như vậy sau khi khớp được `A1 A2 A3 A4 A5` trên đoạn text ta cần tìm chuỗi tiền tố hậu tố dài nhất (longest prefix suffix - LPS) không phải chính nó của `A1 A2 A3 A4 A5` để xác định vị trí tiếp theo có khả năng khớp trên đoạn text. Thêm nữa, sau khi biết được chuỗi tiền tố hậu tố dài nhất của đoạn đã khớp, ở lần khớp tiếp theo ta không cần bắt đầu lại từ vị trí đầu tiên của chuỗi mẫu mà tiếp tục ở vị trí kí tự tiếp theo chưa biết của đoạn text.

Ở ví dụ trên sau khi khớp được `ABABA`, đoạn này có 2 chuỗi tiền tố hậu tố không phải chính nó là `ABA` và `A`, ta chọn đoạn dài nhất `ABA` (độ dài 3) để tìm vị trí có khả năng khớp tiếp theo `K + (5 - 3)`

```[visual](offsetX=10,shape=rect,autoId=1,size=34)
linear(offsetY=10)
... A{"t":"K","c":"green"} B{"c":"green"} A{"c":"green"} B{"c":"green"} A{"c":"green"} ...
linear(offsetY=70,id=2)
 A{"c":"green"} B{"c":"green"} A{"c":"green"} B{"c":"green"} A{"c":"green"} C{"c":"red","t":"^"}

linear(offsetY=10)
... A{"t":"K"} B A{"c":"green","t":"K+2"} B{"c":"green"} A{"c":"green"} ...
linear(offsetY=70,id=2)
   A{"c":"green","id":"1"} B{"c":"green","id":"2"} A{"c":"green","id":"3"} B{"id":"4"} A{"id":"5"} C{"id":"6"}
```

Như vậy trước khi tìm khớp chuỗi mẫu trong đoạn text ta tiến hành xử lý chuỗi mẫu trước (pre-processing), để tìm chuỗi tiền tố hậu tố dài nhất cho tất cả các trường hợp đã khớp được 1 kí tự, 2 kí tự...m kí tự của chuỗi mẫu.

### Chuỗi tiền tố hậu tố dài nhất (LPS)

Ta có chuỗi mẫu độ dài m [P0P1...Pm-1]. Gọi LPS[i] là độ dài chuỗi tiền tố hậu tố lớn nhất của chuỗi [P0P1...Pi] (độ dài i + 1), ta có LPS[0] = 0 vì với chuỗi 1 kí tự, chuỗi tiền tố hậu tố lớn nhất nhỏ hơn chính nó là chuỗi rỗng.

Giả sử đã tính được LPS[0], LPS[1]...LPS[i-1], ta tìm cách tính LPS[i].

Gọi k = LPS[i] (k < i + 1 hay k <= i). Ta có tiền tố [P0P1...Pk-1] là hậu tố của [P0P1...Pi] suy ra `Pk-1 = Pi` và đoạn còn lại tiền tố [P0...Pk-2] là hậu tố của [P0...Pi-1]. Như vậy để tìm đoạn tiền tố hậu tố của [P0...Pi] ta cần tìm đoạn tiền tố hậu tố của [P0...Pi-1] dài nhất [P0...Pl] mà thỏa mãn kí tự tiếp theo `Pl+1 = Pi`, khi đó ta có LPS[i] = k = l + 1.

Để tìm đoạn tiền tố hậu tố này của [P0...Pi-1], trước hết ta xét đoạn tiền tố hậu tố dài nhất (LPS[i-1]) của nó kiểm tra kí tự tiếp theo có bằng `Pi` ko, nếu có thì ta có kết quả nếu không ta tiếp tục xét đoạn tiền tố hậu tố dài thứ 2, rồi thứ 3...cho đến khi thỏa mãn kí tự tiếp theo hoặc trở về đoạn tiền tố hậu tố rỗng.

Gọi đoạn tiền tố hậu tố dài nhất là PS1, dài thứ 2 là PS2, PS3,...Ta nhận thấy:

- PS1 là tiền tố hậu tố dài nhất của [P0...Pi-1] (độ dài l1 = LPS[i-1]).
- PS2 là tiền tố hậu tố dài nhất của PS1 (độ dài l2 = LPS[l1]).
- PS3 là tiền tố hậu tố dài nhất của PS2 (độ dài l3 = LPS[l2]).
- ...

```ts
function longestPrefixSuffix(p: string): number[] {
  const lps = [0]
  for (let i = 1; i < p.length; i++) { // calculate lps[i]
    // find prefix-suffix of p0..pi-1
    // start with the longest
    let k = lps[i-1]
    // stop at empty prefix-suffix or the next character is p[i]
    while (k > 0 && p[k+1] !== p[i]) k = lps[k]

    // got the prefix-suffix length k
    if (p[k+1] === p[i]) lps[i] = k + 1
    else lps[i] = 0
  }

  return lps
}
```

### Áp dụng LPS để tìm khớp xâu con

Sau khi tính được mảng LPS cho tất cả các tiền tố của P, ta áp dụng mảng LPS để tìm khớp xâu mẫu trong đoạn text.

- Khởi tạo vị trí chuẩn bị thử khớp trên đoạn text `start = 0`, số kí tự đã khớp nhờ bước trước `k = 0`
- Vòng lặp bắt đầu với điều kiện tiếp tục `start <= n - m`
- Ta tiếp tục khớp từng kí tự trên đoạn text với chuỗi mẫu và tăng k cho đến khi gặp kí tự sai khác hoặc khớp hoàn toàn.
- Nếu đã khớp hoàn toàn lưu lại kết quả.
- Nếu chưa tiếp tục tính `start` và `k` cho vòng lặp kế tiếp dùng mảng LPS:
  - Nếu k > 0
    - `start = start + (k - lps[k-1])`
    - `k = lps[k-1]`
  - Nếu k = 0 (không khớp được kí tự nào): `start = start + 1`

```ts
function kmp(p: string, t: string): number[] {
  // matched positions
  const rs = []
  // pattern length
  const m = p.length
  // text length
  const n = t.length
  // lps array, length m
  const lps = longestPrefixSuffix(p)
  // init
  let start = 0, k = 0
  while (start <= n - m) {
    while (k < m && t[start + k] === p[k]) k++
    if (k === m) {
      rs.push(start)
    }
    if (k > 0) {
      start += k - lps[k-1]
      k = lps[k-1]
    } else {
      start++
    }
  }

  return rs
}
```

## Minh họa
