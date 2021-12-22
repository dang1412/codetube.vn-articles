# ERC20 Token

## Các khái niệm cơ bản

Để hiểu cách thức hoạt động và cài đặt 1 đồng token trên mạng lưới blockchain trước hết chúng ta nên nắm được các khái niệm cơ bản sau

- Blockchain
- Transaction
- Account
- Smartcontract

### Blockchain

Blockchain là 1 cơ sở dữ liệu phi tập trung được quản lí và vận hành bởi nhiều máy chủ (fullnode) khác nhau 1 cách độc lập. Khác với các cơ sở dữ liệu tập trung truyền thống (Mysql, Postgres, MongoDB...), cơ sở dữ liệu Blockchain là công khai minh bạch và không 1 thực thể nào có khả năng thay đổi dữ liệu theo ý mình.

### Account

Tài khoản trên Blockchain được tạo thành từ 1 cặp khóa public-private key, trong đó public key đóng vai trò là địa chỉ tài khoản còn private key là để tạo chữ ký xác thực quyền sở hữu địa chỉ này (để tạo giao dịch hợp lệ). Trạng thái cơ bản của 1 địa chỉ tài khoản người dùng đó là số dư (Balance). Trạng thái này sẽ thay đổi khi có các giao dịch chuyển tiền từ địa chỉ này sang địa chỉ khác.

### Transaction

Cơ sở dữ liệu Blockchain được tạo thành từ tập các giao dịch (transaction) hợp lệ theo 1 thứ tự xác định. Trạng thái (state) của Blockchain tại 1 thời điểm trong quá khứ là xác định và có thể tính được bằng cách chạy lại (replay) toàn bộ các giao dịch từ đầu cho đến thời điểm đó. Một giao dịch hợp lệ trên Blockchain được tạo thành bao gồm các thông tin sau:

- Địa chỉ gửi (From)
- Địa chỉ đến (To)
- Số coin chuyển, phí giao dịch
- Metadata (note, gọi hàm trong trường hợp To là 1 địa chỉ HDTM...)
- Chữ ký xác thực tương ứng với địa chỉ From đối với toàn bộ nội dung giao dịch ở trên.

### Smartcontract

Về cơ bản hợp đồng thông minh (HDTM) cũng là 1 địa chỉ trên Blockchain như các địa chỉ tài khoản người dùng thông thường, tức là địa chỉ HDTM cũng có khả năng lưu trữ, nhận và chuyển coin. Khác với tài khoản thông thường, hoạt động của HDTM không được điều khiển bằng 1 private key mà được định sẵn bằng code và được đảm bảo vận hành bởi mạng lưới blockchain. Trạng thái (State) của 1 địa chỉ HDTM không chỉ có lưu trữ số dư coin (balance) mà còn có thể là kiểu dữ liệu bất kỳ (biến, mảng, struct,...). Chúng ta có thể hình dung HDTM là 1 tài khoản robot đã được lập trình sẵn, người dùng tương tác bằng cách gửi các giao dịch đến địa chỉ này để update trạng thái của HDTM. Bởi vậy HDTM có thể thay thế phần nào cho các ứng dụng backend truyền thống. Các ứng dụng truyền thống có thể thay thế 1 phần backend bằng HDTM để trở thành các ứng dụng phi tập trung.

## Ứng dụng HDTM để tạo Token ERC20

Trạng thái (State) của HDTM có thể lưu bất kỳ kiểu dữ liệu nào nên chúng ta có thể áp dụng HDTM để xây dựng ứng dụng phi tập trung.

Tạo token trên mạng lưới Blockchain có thể nói là 1 trong những ứng dụng cơ bản và phổ biến nhất hiện nay. Để hiểu cách thức tạo 1 đồng token như thế nào trước hết ta hình dung cách mà chúng ta sẽ làm để tạo đồng tiền này mà không dùng blockchain.

- Lập 1 cơ sở dữ liệu (Mysql, Postgres) có chứa thông tin tài khoản người dùng A sở hữu 1000 token, các tài khoản khác bằng 0.
- Lập 1 máy chủ kết nối vào CSDL trên, cung cấp những API sau
  - Số dư tài khoản bằng cách đọc CSDL.
  - Chuyển tiền từ tài khoản X sang tài khoản Y bằng cách cập nhật CSDL giảm số dư của X và tăng số dư của Y 1 khoản tương ứng.

Lập 1 đồng token trên Blockchain ETH cũng giống hệt như vậy, chỉ khác là dữ liệu được ghi vào State của HDTM và sử dụng sẵn hệ thống account Blockchain. Chuẩn cài đặt ERC20 cho đồng token được áp dụng rộng rãi để thuận tiện cho việc trao đổi giữa các đồng token với nhau. Chúng ta cùng đi vào phần cài đặt cụ thể dùng Solidity.

### Interface

1 token contract cần cung cấp những chức năng sau

```js
interface IERC20 {
  // tổng cung
  function totalSupply() external view returns (uint256);
  // check số dư tài khoản
  function balanceOf(address account) external view returns (uint256);
  // chuyển tiền từ người gọi hàm (msg.sender) đến người nhận
  function transfer(address recipient, uint256 amount) external returns (bool);
  // trả về số lượng token mà chủ sở hữu (owner) cho phép người thứ 3 (spender) sử dụng (spender có quyền chuyển số token này đi)
  function allowance(address owner, address spender) external view returns (uint256);
  // cho phép người thứ 3 (spender) sử dụng số token (amount) của người gọi hàm (msg.sender)
  function approve(address spender, uint256 amount) external returns (bool);
  // người gọi hàm (msg.sender) chuyển số token (amount) từ người gửi (sender) đến người nhận (recipient)
  // người gọi hàm cần có quyền sử dụng số token này của người gửi
  function transferFrom(
      address sender,
      address recipient,
      uint256 amount
  ) external returns (bool);

  // Các sự kiện của contract cho phép ta lắng nghe theo thời gian thực
  event Transfer(address indexed from, address indexed to, uint256 value);
  event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

Ngoài ra 1 token contract cung cấp thêm các thông tin sau

```js
interface IERC20Metadata {
  // tên token, vd Etherium
  function name() external view returns (string memory);

  // biểu tượng của token, vd ETH
  function symbol() external view returns (string memory);

  // 1 token có thể chia nhỏ bao nhiêu.
  // VD 10: 1 token == 10000000000 (10 số 0)
  // Con số hay gặp là 18
  function decimals() external view returns (uint8);
}
```

### Cài đặt

Giá trị khởi tạo lúc deploy (constructor), State của contract cần có những biến lưu thông tin sau

```js
contract ERC20 is IERC20, IERC20Metadata {
  mapping(address => uint256) private _balances;

  mapping(address => mapping(address => uint256)) private _allowances;

  uint256 private _totalSupply;

  string private _name;
  string private _symbol;

  /**
    * @dev Sets the values for {name} and {symbol}.
    *
    * The default value of {decimals} is 18. To select a different value for
    * {decimals} you should overload it.
    *
    * All two of these values are immutable: they can only be set once during
    * construction.
    */
  constructor(string memory name_, string memory symbol_) {
      _name = name_;
      _symbol = symbol_;
  }
}
```


