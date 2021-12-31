# ERC20 Token

Token ERC20 là 1 trong những ứng dụng Blockchain cơ bản và phổ biến nhất hiện nay.

## Các khái niệm cơ bản

Để hiểu cách thức hoạt động và cài đặt 1 đồng token trên mạng lưới Blockchain trước hết ta nhắc lại sơ qua các khái niệm sau

- Blockchain
- Transaction
- Account
- Smartcontract
- ERC20

### Blockchain

Blockchain là 1 cơ sở dữ liệu phi tập trung được quản lí và vận hành bởi nhiều máy chủ (fullnode) khác nhau 1 cách độc lập. Khác với các cơ sở dữ liệu tập trung truyền thống (Mysql, Postgres, MongoDB...), cơ sở dữ liệu Blockchain là công khai minh bạch và không 1 đơn vị tập trung nào có khả năng thay đổi dữ liệu theo ý mình.

### Account

Tài khoản trên Blockchain được tạo thành từ 1 cặp khóa public-private key, trong đó public key đóng vai trò là địa chỉ tài khoản còn private key là để tạo chữ ký xác thực quyền sở hữu địa chỉ này. Trạng thái cơ bản của 1 địa chỉ tài khoản người dùng đó là số dư (Balance). Trạng thái này sẽ thay đổi khi có các giao dịch chuyển tiền từ địa chỉ này sang địa chỉ khác.

### Transaction

Cơ sở dữ liệu Blockchain được tạo thành từ tập các giao dịch (transaction) hợp lệ theo 1 thứ tự xác định. Trạng thái (state) của Blockchain tại 1 thời điểm trong quá khứ là xác định và có thể tính được bằng cách chạy lại (replay) toàn bộ các giao dịch từ đầu cho đến thời điểm đó. Một giao dịch hợp lệ trên Blockchain được tạo thành bao gồm các thông tin sau:

- Địa chỉ gửi (From)
- Địa chỉ đến (To)
- Số coin chuyển
- Metadata (note, gọi hàm trong trường hợp To là 1 địa chỉ HDTM...)
- Chữ ký xác thực tương ứng với địa chỉ From đối với toàn bộ nội dung giao dịch ở trên.

### Smartcontract

Về cơ bản hợp đồng thông minh (HDTM) là 1 địa chỉ trên Blockchain như các địa chỉ tài khoản người dùng thông thường khác, tức là địa chỉ HDTM cũng có khả năng lưu trữ, nhận và chuyển coin. Khác với tài khoản thông thường, hoạt động của HDTM không được điều khiển bằng 1 private key mà được định sẵn bằng code và được đảm bảo vận hành bởi mạng lưới blockchain. Trạng thái (State) của 1 địa chỉ HDTM không chỉ có lưu trữ số dư coin (balance) mà còn có thể là kiểu dữ liệu bất kỳ (biến, mảng, struct,...). Chúng ta có thể hình dung HDTM là 1 tài khoản robot đã được lập trình sẵn, người dùng tương tác bằng cách gửi các giao dịch đến địa chỉ này để update trạng thái của HDTM. Các ứng dụng truyền thống có thể thay thế 1 phần backend bằng HDTM để trở thành các ứng dụng phi tập trung.

### Ứng dụng HDTM để tạo Token ERC20

Tạo token trên mạng lưới Blockchain có thể nói là 1 trong những ứng dụng cơ bản và phổ biến cũng như dễ tiếp cận nhất hiện nay. Để hiểu cách thức tạo 1 đồng token như thế nào trước hết ta hình dung cách mà chúng ta sẽ làm để tạo đồng tiền này mà không dùng blockchain.

- Lập 1 cơ sở dữ liệu (Mysql, Postgres) có bảng chứa thông tin tài khoản người dùng A sở hữu 1000 token, các tài khoản khác bằng 0.
- Lập 1 máy chủ kết nối vào CSDL trên, cung cấp API
  - Số dư tài khoản bằng cách đọc CSDL.
  - Chuyển tiền từ tài khoản X sang tài khoản Y bằng cách cập nhật CSDL giảm số dư của X và tăng số dư của Y 1 khoản tương ứng.

Lập 1 đồng token trên Blockchain cũng giống hệt như vậy, chỉ khác là dữ liệu được ghi trực tiếp vào State của HDTM và sử dụng hệ thống account Blockchain có sẵn. Hay nói cách khác HDTM là backend phi tập trung chứa cả logic API và Database được cài đặt ở cùng 1 chỗ, và 1 khi đã deploy database cũng như logic này không thể tùy ý thay đổi như ở ứng dụng truyền thống. Chuẩn cài đặt ERC20 cho đồng token được áp dụng rộng rãi để thuận tiện cho việc trao đổi, hiển thị...các đồng token trong các ứng dụng khác.

## Thuật toán

Chúng ta cùng đi vào cài đặt phần logic chính của ERC20 dùng [Solidity](https://docs.soliditylang.org/).

### Interface

Ngoài việc thực hiện chuyển tiền đơn thuần giữa 2 người, ERC20 cho phép 1 tài khoản bên thứ 3 thực hiện chuyển tiền giữa 2 tài khoản khác nhau, với điều kiện là chủ sở hữu tài khoản gửi cho phép. Token contract cung cấp những chức năng thể hiện ở giao diện sau:

```sol
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

  // Các sự kiện của contract cho phép lắng nghe theo thời gian thực
  event Transfer(address indexed from, address indexed to, uint256 value);
  event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

Ngoài ra 1 token contract cung cấp thêm các thông tin sau

```sol
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

Data của token contract cần ghi thông tin sau

```sol
contract ERC20 is IERC20, IERC20Metadata {
  // Số dư
  mapping(address => uint256) private _balances;

  // Cho phép sử dụng
  mapping(address => mapping(address => uint256)) private _allowances;
}
```

Trả về số dư tài khoản

```sol
function balanceOf(address account) public view returns (uint256) {
  return _balances[account];
}
```

Chuyển token từ người gọi contract

```sol
// Người gọi thực hiện chuyển token
function transfer(address recipient, uint256 amount) public returns (bool) {
    _transfer(_msgSender(), recipient, amount);
    return true;
}

// Hàm con private thực hiện chuyển token từ tài khoản gửi sang tài khoản nhận
function _transfer(
  address sender,
  address recipient,
  uint256 amount
) internal {
  require(sender != address(0), "ERC20: transfer from the zero address");
  require(recipient != address(0), "ERC20: transfer to the zero address");

  uint256 senderBalance = _balances[sender];
  require(senderBalance >= amount, "ERC20: transfer amount exceeds balance");

  _balances[sender] = senderBalance - amount;
  _balances[recipient] += amount;

  // emit event transfer
  emit Transfer(sender, recipient, amount);
}
```

Để bên thứ 3 (người gọi contract) thực hiện chuyển token từ người A sang người B, chúng ta cần thông tin chủ sở hữu token cho phép người thứ 3 sử dụng bao nhiêu đồng token.

```sol
// Chứa thông tin cho phép sử dụng
mapping(address => mapping(address => uint256)) private _allowances;

// Cấp phép sử dụng
function _approve(
  address owner,
  address spender,
  uint256 amount
) internal virtual {
  require(owner != address(0), "ERC20: approve from the zero address");
  require(spender != address(0), "ERC20: approve to the zero address");

  // "owner" cho phép "spender" sử dụng "amount" đồng token của owner
  _allowances[owner][spender] = amount;
}
```

Người gọi contract thực hiện chuyển token từ người gửi đến người nhận

```sol
function transferFrom(
  address sender,
  address recipient,
  uint256 amount
) public returns (bool) {
  // thực hiện chuyển
  _transfer(sender, recipient, amount);

  // kiểm tra lượng token cho phép lớn hơn lượng token chuyển
  uint256 currentAllowance = _allowances[sender][_msgSender()];
  require(currentAllowance >= amount, "ERC20: transfer amount exceeds allowance");

  // update giảm số lượng cho phép sau khi chuyển
  _approve(sender, _msgSender(), currentAllowance - amount);

  // event Approval
  emit Approval(owner, spender, amount);

  return true;
}
```

Ngoài ra chúng ta có hàm `internal` `_mint` để tạo token mới và thêm vào 1 địa chỉ

```sol
function _mint(address account, uint256 amount) internal virtual {
  require(account != address(0), "ERC20: mint to the zero address");

  _totalSupply += amount;
  _balances[account] += amount;

  // event Transfer
  emit Transfer(address(0), account, amount);
}
```

## Setup và deploy với Hardhat

Trên thực tế để tạo 1 đồng token của riêng mình chúng ta không cần code lại ERC20 mà có thể sử dụng thư viện mở có sẵn [Openzeppellin-ERC20](https://docs.openzeppelin.com/contracts/4.x/erc20).

```sol
// contracts/GLDToken.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract GLDToken is ERC20 {
  constructor(uint256 initialSupply) ERC20("Gold", "GLD") {
    // tạo toàn bộ token và gán quyền sở hữu cho người deploy contract
    _mint(msg.sender, initialSupply);
  }
}
```

Setup, test và deploy dùng Hardhat và Typescript.

### Setup

```sh
mkdir MyToken
cd MyToken
npm init # Làm theo các bước khởi tạo npm project
npx hardhat # Chọn options tạo project với typescript và làm theo các bước khởi tạo
# Cài đặt các package theo hướng dẫn
# npm i --save-dev hardhat @nomiclabs/hardhat-waffle typechain...

# Cài đặt openzeppellin contracts
npm i --save-dev @openzeppelin/contracts
```

Sau khi khởi tạo project và cài đặt các gói cần thiết, tạo file contract cho token `contracts/GLDToken.sol`

```sol
// contracts/GLDToken.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract GLDToken is ERC20 {
  constructor(uint256 initialSupply) ERC20("Gold", "GLD") {
    _mint(msg.sender, initialSupply);
  }
}
```

Compile contract, hardhat sẽ tự động sinh ra kiểu cho contract (dùng `typechain` package) để chúng ta có thể test với typescript rất tiện lợi.

```sh
npx hardhat compile
```

### Test

Để test tạo file `test/GLDToken.spec.ts`, sử dụng cấu trúc `Mocha`

```ts
import { expect } from "chai"
import { ethers } from "hardhat"
import { GLDToken__factory, GLDToken } from "../typechain"

const InitSupply = 1000

describe("MyToken", async () => {
  // Lấy 3 account mà hardhat cung cấp
  const [owner, acc1, acc2] = await ethers.getSigners()
  // contract instance
  let GLDToken: GLDToken__factory
  let gldToken: GLDToken

  // Trước mỗi testcase deploy 1 contract mới để test để đảm bảo các testcase chạy độc lập với nhau
  beforeEach(async () => {
    GLDToken = await ethers.getContractFactory("GLDToken")
    gldToken = await GLDToken.deploy(InitSupply)
    await gldToken.deployed()
  })

  it("Should deploy with initial supply", async () => {
    expect(await gldToken.balanceOf(owner.address)).to.equal(1000)
  })

  // test transfer
  describe("transfer", () => {
    it("Should transfer to acc1", async () => {
      await gldToken.transfer(acc1.address, 100)
      expect(await gldToken.balanceOf(owner.address)).to.equal(900)
      expect(await gldToken.balanceOf(acc1.address)).to.equal(100)

      gldToken.on("Transfer", (from: string, to: string, amount: number) => {
        console.log("Event emitted", from, to, amount)
      })
    })

    it("Should revert when transfer amount exceeds", async () => {
      await expect(gldToken.transfer(acc1.address, 1001)).to.be.revertedWith("ERC20: transfer amount exceeds balance")
      expect(await gldToken.balanceOf(owner.address)).to.equal(1000)
      expect(await gldToken.balanceOf(acc1.address)).to.equal(0)
    })
  })

  // test approve và transferFrom
  describe("When approved allownance", () => {
    beforeEach(async () => {
      gldToken.approve(acc1.address, 150)
    })

    it("Should allow 150", async () => {
      expect(await gldToken.allowance(owner.address, acc1.address)).to.equal(150)
    })

    it("Should revert when try transferFrom larger than approved", async () => {
      await expect(gldToken.connect(acc1).transferFrom(owner.address, acc2.address, 160)).to.be.revertedWith("ERC20: transfer amount exceeds allowance")
    })

    it("Should transferFrom owner to acc2 by acc1", async () => {
      await gldToken.connect(acc1).transferFrom(owner.address, acc2.address, 100)

      // check balance
      expect(await gldToken.balanceOf(owner.address)).to.equal(900)
      expect(await gldToken.balanceOf(acc2.address)).to.equal(100)

      // check remaining allownance
      expect(await gldToken.allowance(owner.address, acc1.address)).to.equal(50)
    })
  })
})
```

Chạy test

```sh
npx hardhat test
```

### Deploy

Khi chạy test mà không chỉ định network, Hardhat khởi chạy 1 chuỗi mặc định gọi là `hardhat network` và tự động dừng khi test run kết thúc. Để deploy lên network có sẵn như local chain hay testnet, trước hết ta phải khai báo network này trong config file `hardhat.config.ts`.

Khai báo endpoint url (sử dụng node của `alchemy`) và private key trong file `.env`

```sh
ROPSTEN_URL=https://eth-ropsten.alchemyapi.io/v2/<YOUR ALCHEMY KEY>
PRIVATE_KEY=0xabc123abc123abc123abc123abc123abc123abc123abc123abc123abc123abc1
```

Trong config file, khai báo ropsten testnet:

```ts
const config: HardhatUserConfig = {
  solidity: "0.8.4",
  networks: {
    ropsten: {
      url: process.env.ROPSTEN_URL || "",
      accounts:
        process.env.PRIVATE_KEY !== undefined ? [process.env.PRIVATE_KEY] : [],
    },
  },
}
```

Tạo script trong `scripts/deploy.ts`

```ts
import { ethers } from "hardhat"

async function main() {
  // Hardhat always runs the compile task when running scripts with its command
  // line interface.
  //
  // If this script is run directly using `node` you may want to call compile
  // manually to make sure everything is compiled
  // await hre.run('compile');

  // We get the contract to deploy
  const GLDToken = await ethers.getContractFactory("GLDToken")
  const gLDToken = await GLDToken.deploy("Hello, Hardhat!")

  await gLDToken.deployed()

  console.log("GLDToken deployed to:", gLDToken.address)
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error)
  process.exitCode = 1
})
```

Deploy lên ropsten testnet

```sh
npx hardhat run --network ropsten scripts/deploy.ts
```

Hiểu được cài đặt và vận hành của đồng token ERC20 là tiền đề để chúng ta tiếp tục tìm hiểu các ứng dụng phức tạp hơn như Swap, NFT marketplace...trong các bài tiếp theo.
