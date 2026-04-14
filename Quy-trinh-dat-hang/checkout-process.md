# process-diagram_web01
Sơ đồ quy trình hệ thống

### 1. Giai đoạn Xác thực
```mermaid
graph TD
    Start((Bắt đầu)) --> A[Người dùng ở trang Giỏ hàng]
    A --> B{Đã đăng nhập?}
    B -- Chưa --> C[Chuyển hướng trang Đăng nhập]
    C --> A
    B -- Rồi --> D[GET /checkout: Hiển thị Form Đặt Hàng]
```
### 2. Giai đoạn kiểm tra logic
```mermaid
graph TD
    E[Người dùng điền Form & Bấm Đặt Hàng] --> F[POST /checkout/process]
    F --> G{Validate Form?}
    G -- Lỗi rỗng --> H[Báo lỗi, quay lại Form]
    G -- Hợp lệ --> I[Model: Lấy giỏ hàng & Tính lại Tổng tiền]
    I --> J{Kiểm tra Tồn kho?}
    J -- Hết hàng --> K[Báo lỗi, dừng quy trình]
```
### 3. Giai đoạn Transaction Database
```mermaid
graph TD
    J[Đủ hàng] --> L((BẮT ĐẦU TRANSACTION DB))
    L --> M[1. INSERT don_hang]
    M --> N[2. Lặp: INSERT chi_tiet_don_hang]
    N --> O[3. UPDATE phien_ban_san_pham <br/> Trừ tồn kho]
    O --> P[4. DELETE chi_tiet_gio_hang <br/> Xóa giỏ hàng]
    P --> Q((COMMIT TRANSACTION))
```
### 4. Giai đoạn thanh toán và hoàn tất
```mermaid
graph TD
    Q[Commit thành công] --> R{Phương thức thanh toán?}
    R -- Online --> S[Tạo chữ ký số & Chuyển hướng Cổng thanh toán]
    S --> T[Cổng thanh toán gọi IPN cập nhật 'ĐÃ THANH TOÁN']
    
    R -- COD --> U[Gửi Email xác nhận qua PHPMailer]
    U --> V[Xóa Session tạm & Chuyển hướng]
    
    T --> W([Trang Đặt Hàng Thành Công])
    V --> W
```
