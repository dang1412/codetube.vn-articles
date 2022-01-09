# Token ERC721 với Ownable và Enumerable

ERC20 là chuẩn dành cho những đồng token có tính chất giống hệt nhau (fungible token), giống như 1 đồng USD có giá trị hoàn toàn tương đương với 1 đồng USD bất kỳ nào khác. Đối với các loại tài sản có tính chất riêng biệt độc nhất (Non Fungible Token - NFT) như mỗi mảnh đất, mỗi vật phẩm, nhân vật trong game..., chúng ta có chuẩn ERC721 để số hóa các loại tài sản như vậy trên Blockchain.

Bài viết này mình sẽ giải thích cơ chế hoạt động của token ERC721 cơ bản và chức năng mở rộng ERC721Enumerable, cùng với đó là demo tạo 1 contract NFT đơn giản có thể áp dụng thực tiễn.

## ERC721 cơ bản

Smartcontract của token [ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol) gồm các dữ liệu và cài đặt cơ bản sau

### Data

```sol
  // Map đồng token => chủ sở hữu
  mapping(uint256 => address) private _owners;

  // Map account => số token sở hữu
  mapping(address => uint256) private _balances;

  // Map token => account có quyền sử dụng (ngoài chủ sở hữu)
  mapping(uint256 => address) private _tokenApprovals;

  // Map account => list những account được cấp quyền (cho tất cả token mà nó sở hữu)
  mapping(address => mapping(address => bool)) private _operatorApprovals;
```

### Các methods

Hàm nội bộ chuyển đồng token từ account `from` sang account `to`

```sol
/**
  * @dev Transfers `tokenId` from `from` to `to`.
  *  As opposed to {transferFrom}, this imposes no restrictions on msg.sender.
  *
  * Requirements:
  *
  * - `to` cannot be the zero address.
  * - `tokenId` token must be owned by `from`.
  *
  * Emits a {Transfer} event.
  */
function _transfer(
  address from,
  address to,
  uint256 tokenId
) internal virtual {
  // Kiểm tra token thuộc sở hữu của acc 'from'
  require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer from incorrect owner");
  require(to != address(0), "ERC721: transfer to the zero address");

  // Hàm hook trước khi thực hiện chuyển token
  _beforeTokenTransfer(from, to, tokenId);

  // Xóa approval khi token đổi chủ
  _approve(address(0), tokenId);

  // Cập nhật số dư
  _balances[from] -= 1;
  _balances[to] += 1;
  // Cập nhật chủ mới cho token
  _owners[tokenId] = to;

  // Event chuyển token
  emit Transfer(from, to, tokenId);

  // Hàm hook sau khi chuyển
  _afterTokenTransfer(from, to, tokenId);
}
```

Hàm nội bộ thực hiện check quyền sử dụng token

```sol
/**
  * @dev Returns whether `spender` is allowed to manage `tokenId`.
  *
  * Requirements:
  *
  * - `tokenId` must exist.
  */
function _isApprovedOrOwner(address spender, uint256 tokenId) internal view virtual returns (bool) {
  // Kiểm tra token tồn tại
  require(_exists(tokenId), "ERC721: operator query for nonexistent token");
  // Lấy account chủ sở hữu (khi gọi hàm public thêm namespace để dễ phân biệt với hàm internal)
  address owner = ERC721.ownerOf(tokenId);
  return (spender == owner || getApproved(tokenId) == spender || isApprovedForAll(owner, spender));
}
```

Hàm public thực hiện check điều kiện trước khi chuyển token

```sol
/**
  * @dev See {IERC721-transferFrom}.
  */
function transferFrom(
  address from,
  address to,
  uint256 tokenId
) public virtual override {
  //solhint-disable-next-line max-line-length
  require(_isApprovedOrOwner(_msgSender(), tokenId), "ERC721: transfer caller is not owner nor approved");

  _transfer(from, to, tokenId);
}
```

Hàm internal `_mint` thực hiện tạo token mới

```sol
/**
  * @dev Mints `tokenId` and transfers it to `to`.
  *
  * WARNING: Usage of this method is discouraged, use {_safeMint} whenever possible
  *
  * Requirements:
  *
  * - `tokenId` must not exist.
  * - `to` cannot be the zero address.
  *
  * Emits a {Transfer} event.
  */
function _mint(address to, uint256 tokenId) internal virtual {
  require(to != address(0), "ERC721: mint to the zero address");
  require(!_exists(tokenId), "ERC721: token already minted");

  _beforeTokenTransfer(address(0), to, tokenId);

  _balances[to] += 1;
  _owners[tokenId] = to;

  emit Transfer(address(0), to, tokenId);

  _afterTokenTransfer(address(0), to, tokenId);
}
```

Ở đây ta thấy 1 token chỉ có mỗi thuộc tính là tokenId. Thường thì mỗi token sẽ được thêm thuộc tính bằng cách gắn thêm 1 đường link `uri` đến file json chứa thông tin cụ thể của nó. Đường link này được xác định bằng hàm

```sol
// link đến thông tin cụ thể của token
function tokenURI(uint256 tokenId) public view virtual override returns (string memory) {
  require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");

  string memory baseURI = _baseURI();
  return bytes(baseURI).length > 0 ? string(abi.encodePacked(baseURI, tokenId.toString())) : "";
}

// override hàm này khi cài đặt để set uri cho token
function _baseURI() internal view virtual returns (string memory) {
  return "";
}
```

Ngoài ra chúng ta có hàm `safeMint`, `safeTransferFrom` thực hiện check địa chỉ `to` nếu là smartcontract thì contract này phải cài đặt interface (`IERC721Receiver` hàm `onERC721Received`) để nhận token nếu không giao dịch sẽ bị revert. Chức năng này đảm bảo token không bị chuyển nhầm đến 1 smartcontract ngẫu nhiên nào đó dẫn đến token bị mất vĩnh viễn (do token thuộc về 1 địa chỉ smartcontract mà không có chức năng chuyển đi). Và 1 số các hàm khác các bạn xem cụ thể trong source code của [OpenZeppelin ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol).

## ERC721Enumerable

Chức năng mở rộng `Enumerable` [ERC721Enumerable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol) thực hiện lưu mảng tất cả token và danh sách các token cụ thể mà mỗi account sở hữu.

```sol
  // Map account => danh sách các đồng mà nó sở hữu ('mapping(uint256 => uint256)' ở đây mang ý nghĩa là 1 array)
  mapping(address => mapping(uint256 => uint256)) private _ownedTokens;

  // Map tokenId => số thứ tự của nó trong danh sách của chủ sở hữu
  mapping(uint256 => uint256) private _ownedTokensIndex;

  // Mảng chứa tất cả các tokenId
  uint256[] private _allTokens;

  // Map tokenId => số thứ tự của nó trong mảng tất cả các đồng
  mapping(uint256 => uint256) private _allTokensIndex;
```

Những data này cho phép chúng ta duyệt qua tất cả các token hiện có hay duyệt qua các token mà 1 account sở hữu 1 cách thuận tiện, và tất nhiên sẽ tốn thêm chi phí cho việc lưu trữ này trên Blockchain.

Đây là contract mở rộng của contract ERC721 ở trên, cài đặt hook `_beforeTokenTransfer` để thực hiện cập nhật dữ liệu mảng ở trên mỗi khi có `mint`, `burn` hoặc chuyển token giữa các account. Giải thuật cơ bản để thêm xóa mảng đều có độ phức tạp O(1):

- Thêm: push vào cuối mảng
- Xóa:
  - Ghi đè phần tử cuối lên vị trí cần xóa
  - Xóa phần tử cuối

Bởi vậy ta thấy thứ tự các đồng token trong mảng có thể bị xáo trộn, ta sẽ thấy rõ hơn điều này khi test ở phần sau. Cài đặt `ERC721Enumerable` cung cấp các hàm sau:

```sol
interface IERC721Enumerable is IERC721 {

  // tổng cung hiện tại
  function totalSupply() public view virtual override returns (uint256) {
    return _allTokens.length;
  }

  // trả về tokenId của 'owner' ở ví trí 'index' 
  // vd tokenOfOwnerByIndex(aAddress, 0), tokenOfOwnerByIndex(aAddress, 1)...
  function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {
    require(index < ERC721.balanceOf(owner), "ERC721Enumerable: owner index out of bounds");
    return _ownedTokens[owner][index];
  }

  // trả về tokenId ở vị trí 'index' trong danh sách all tokens
  // vd tokenByIndex(0), tokenByIndex(1)
  function tokenByIndex(uint256 index) public view virtual override returns (uint256) {
    require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
    return _allTokens[index];
  }
}
```

## Cài đặt Token ERC721 với Ownable và Enumerable

Sau khi hiểu cách hoạt động của 1 contract token ERC721, phần này ta đi vào cài đặt 1 contract token với yêu cầu cụ thể:

- Người dùng trả phí thực hiện mint token với tokenId lần lượt tăng dần bắt đầu từ 1.
- Tổng cung không vượt quá 1000 (token id lớn nhất là 1000).
- Set uri cho từng token `www.mygame.com/{tokenId}`.
- Admin có quyền set giá mint token
- Admin rút native token (tiền thu được khi user thực hiện mint) từ contract.

Chức năng admin ta sử dụng abstract contract [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) với account deploy contract được set mặc định là account admin (`owner`), sử dụng modifier `onlyOwner` với những hàm đòi hỏi quyền admin.

### Code

```sol
// contracts/TokenERC721.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract TokenERC721 is Ownable, ERC721Enumerable {
  uint256 public mintPrice = 1000000000000000000;  // 1 native token (1ETH - 18 decimal)
  string private _baseUri;

  constructor() ERC721("TokenERC721", "TKE") {}

  function _baseURI() internal view override returns (string memory) {
    return _baseUri;
  }

  function setBaseURI(string calldata baseURI) external onlyOwner {
    _baseUri = baseURI;
  }

  function setPrice(uint256 price) external onlyOwner {
    mintPrice = price;
  }

  function mint() external payable {
    uint256 id = ERC721Enumerable.totalSupply() + 1;
    require(id <= 1000, "Max supply reached");
    require(msg.value >= mintPrice, "Not pay enough money");

    _mint(msg.sender, id);
  }

  function withdraw() external onlyOwner {
    payable(msg.sender).transfer(address(this).balance);
  }
}
```

### Test

Thông thường chúng ta chỉ cần test những chức năng mà mình tự cài đặt nhưng ở đây mình sẽ test thử cả 1 số chức năng trong thư viện `Openzeppellin` để nhìn thấy hoạt động của nó rõ hơn. Trước mỗi testcase ta lấy 3 account đc hardhat cung cấp sẵn, deploy contract mới để đảm bảo các testcase độc lập với nhau, đồng thời thực hiện mint 3 token, 1 token, 1 token lần lượt cho 3 account này.

```ts
describe("LandToken721", () => {
  let [acc1, acc2, acc3]: SignerWithAddress[] = []
  let LandToken721: LandToken721__factory
  let landToken721: LandToken721

  beforeEach(async () => {
    [acc1, acc2, acc3] = await ethers.getSigners()
    LandToken721 = await ethers.getContractFactory("LandToken721")
    landToken721 = await LandToken721.deploy()
    await landToken721.deployed()

    const value = await landToken721.mintPrice()
    await landToken721.mint({ value })  // TokenID 1
    await landToken721.mint({ value })  // 2
    await landToken721.mint({ value })  // 3
    await landToken721.connect(acc2).mint({ value })  // 4
    await landToken721.connect(acc3).mint({ value })  // 5
  })
})
```

Test chức năng Ownable

```ts
describe("Ownable", () => {
  it("Owner should be acc1", async () => {
    const owner = await landToken721.owner()
    expect(owner).to.equal(acc1.address)
  })

  it("Should transfer ownership to acc2", async () => {
    await landToken721.transferOwnership(acc2.address)
    const owner = await landToken721.owner()
    expect(owner).to.equal(acc2.address)
  })
})
```

Test chức năng ERC721 cơ bản

```ts
describe("ERC721", () => {
  it("Check balances", async () => {
    expect(await landToken721.balanceOf(acc1.address)).to.equal(3)
    expect(await landToken721.balanceOf(acc2.address)).to.equal(1)
    expect(await landToken721.balanceOf(acc3.address)).to.equal(1)
    expect(await ethers.provider.getBalance(landToken721.address)).to.equal(5e18.toString())
  })

  it("Accounts should own tokens", async () => {
    expect(await landToken721.ownerOf(1)).to.equal(acc1.address)
    expect(await landToken721.ownerOf(2)).to.equal(acc1.address)
    expect(await landToken721.ownerOf(3)).to.equal(acc1.address)
    expect(await landToken721.ownerOf(4)).to.equal(acc2.address)
    expect(await landToken721.ownerOf(5)).to.equal(acc3.address)
  })

  it("Should transfer token from acc1 to acc2", async () => {
    await landToken721.transferFrom(acc1.address, acc2.address, 1)

    expect(await landToken721.balanceOf(acc1.address)).to.equal(2)
    expect(await landToken721.balanceOf(acc2.address)).to.equal(2)
    expect(await landToken721.ownerOf(1)).to.equal(acc2.address)
  })

  it("Should throw error when transfer nonexistent", async () => {
    await expect(landToken721.transferFrom(acc1.address, acc2.address, 9)).to.be.revertedWith("ERC721: operator query for nonexistent token")
  })

  it("Should throw error when transfer not owned token", async () => {
    await expect(landToken721.transferFrom(acc3.address, acc2.address, 4)).to.be.revertedWith("ERC721: transfer caller is not owner nor approved")
  })

  it("Should transfer not owned token when approved", async () => {
    await landToken721.connect(acc3).approve(acc1.address, 5)
    await landToken721.transferFrom(acc3.address, acc2.address, 5)

    expect(await landToken721.balanceOf(acc3.address)).to.equal(0)
    expect(await landToken721.balanceOf(acc2.address)).to.equal(2)
    expect(await landToken721.ownerOf(5)).to.equal(acc2.address)
  })

  it("Should transfer not owned token when approved all", async () => {
    await landToken721.setApprovalForAll(acc2.address, true)
    await landToken721.connect(acc2).transferFrom(acc1.address, acc2.address, 1)

    expect(await landToken721.balanceOf(acc1.address)).to.equal(2)
    expect(await landToken721.balanceOf(acc2.address)).to.equal(2)
    expect(await landToken721.ownerOf(1)).to.equal(acc2.address)
  })

  it("Token URI", async () => {
    await landToken721.setBaseURI("www.mygame.com/token/")

    expect(await landToken721.tokenURI(1)).to.equal("www.mygame.com/token/1")
    expect(await landToken721.tokenURI(2)).to.equal("www.mygame.com/token/2")
    expect(await landToken721.tokenURI(3)).to.equal("www.mygame.com/token/3")
  })
})
```

Ở đây ta set `baseURI` cho token là `www.mygame.com/token/`, bạn có thể tạo ra list các file json hoặc API trên server của riêng mình để gán thuộc tính cho NFT 1 cách offchain.

```json
// www.mygame.com/token/1
{
  "id": 1,
  "des": "NFT số 1",
  "image": "https://..."
}
```

Để test chức năng `Enumerable` trước hết ta tạo 1 hàm trả về mảng các token bằng cách fetch lần lượt từng token của account và đưa vào 1 mảng

```ts
async function ownedTokensArray(landToken721: LandToken721, addr: string): Promise<number[]> {
  const balance = (await landToken721.balanceOf(addr)).toNumber()
  const tokens: number[] = []
  for (let i = 0; i < balance; i++) {
    const tokenId = (await landToken721.tokenOfOwnerByIndex(addr, i)).toNumber()
    tokens.push(tokenId)
  }

  return tokens
}
```

Test Enumerable

```ts
describe("ERC721 Enumerable", () => {
  it("Total supply", async () => {
    expect(await landToken721.totalSupply()).to.equal(5)
  })

  it("Owned tokens array", async () => {
    expect(await ownedTokensArray(landToken721, acc1.address)).to.eql([1,2,3])
    expect(await ownedTokensArray(landToken721, acc2.address)).to.eql([4])
    expect(await ownedTokensArray(landToken721, acc3.address)).to.eql([5])
  })

  it("Should transfer token from acc1 to acc2", async () => {
    await landToken721.transferFrom(acc1.address, acc2.address, 1)

    // Thứ tự token đảo lại là [3,2] chứ không phải [2,3]
    expect(await ownedTokensArray(landToken721, acc1.address)).to.eql([3,2])
    expect(await ownedTokensArray(landToken721, acc2.address)).to.eql([4,1])
  })
})
```

Full [Testcode](https://github.com/dang1412/Sample-Smartcontract/blob/master/test/LandToken721.spec.ts)

## Kết luận

Nếu bạn là artist và có khả năng tạo ra các bức hình nghệ thuật dạng số thì có thể áp dụng cách này để tạo ra bộ sưu tập giới hạn số lượng cho phép user trả phí để mint. Ngoài ra có thể áp dụng thêm tính năng như tiền bản quyền ([Royalty](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/common/ERC2981.sol)) cho người đã mint mỗi khi NFT được bán lại để tăng tính hấp dẫn...

Đọc hiểu 1 NFT smartcontract giúp bạn dễ dàng tạo bot để crop khi 1 dự án NFT hot ra mắt, hay có thể tạo 1 NFT marketplace cho riêng mình.
