# ERC20 Token

Bài viết này giải thích cách tạo ra 1 đồng token trên mạng lưới ETH (cũng như những mạng lưới khác tương thích sử dụng Solidity như BSC, Avalanche,...)

## Các khái niệm cơ bản

Để tạo và hiểu cách thức vận hành của 1 đồng token trên blockchain trước hết chúng ta nên nắm đc các khái niệm cơ bản sau

- Blockchain
- Transaction
- Smart contract

### Blockchain

Blockchain là cơ sở dữ liệu phi tập trung được vận hành bởi nhiều node khác nhau 1 cách độc lập theo luật đã được lập trình sẵn. Không 1 thực thể đơn lẻ nào có quyền thay đổi cơ sở dữ liệu blockchain theo ý mình như ở các cơ sở dữ liệu truyền thống (mysql, postgres, mongodb...)

### Transaction

Blockchain được tạo thành bởi các giao dịch hợp lệ theo 1 thứ tự xác định

### Smartcontract

Trên mạng lưới blockchain chúng ta có các tài khoản người dùng 
Smartcontract hay hợp đồng thông minh là 
