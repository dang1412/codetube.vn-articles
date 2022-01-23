# Notes hữu ích về Solidity

- Contract Solidity khi dịch ra bytecode không được quá 24kb nếu không giao dịch deploy contract sẽ bị reject.

- EVM - Etherium Virtual Machine là chương trình thực thi bytecode (sau khi được dịch ra từ Solidity hay Rust).

- Thư viện (Library) dùng trong contract sẽ đc compile và deploy ra 1 địa chỉ riêng khác với địa chỉ deploy contract. Sau đó trong contract bytecode, ở các vị trí gọi đến hàm **public** của thư viện sẽ được link đến địa chỉ deploy của thư viện và gọi hàm bằng cách sử dụng opcode `delegatecall` của EVM.

- Đối với các hàm **internal** của thư viện được dùng trong contract, bytecode của hàm sẽ được nhúng trực tiếp vào trong contract, và contract gọi đến hàm bằng cách dùng opcode `JUMP` giống như gọi các hàm internal bên trong contract (khác với việc sử dụng `delagatecall` đối với hàm public của thư viện). Lưu ý là chỉ những hàm internal được gọi mới được nhúng vào contract bytecode (maximum 24kb).

- Selector

```js
bytes4(keccak256('supportsInterface(bytes4)')) == 0x01ffc9a7
```


