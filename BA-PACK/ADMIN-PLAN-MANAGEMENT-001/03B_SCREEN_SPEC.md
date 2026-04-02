# Đặc tả màn hình — PLAN-MANAGEMENT-001 — v1.0
**Cập nhật lần cuối:** 2026-04-02
**Liên quan:** Quản lý gói bảo hiểm (Plan Management), Cấu hình giá (Price Settings)

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-PL-01 | Plan List | Screen | Administrator | Sidemenu > Plan > Plan Management | -> SCR-PL-02, SCR-PL-03 |
| SCR-PL-02 | Create Plan | Screen (New Page) | Administrator | SCR-PL-01 (Nút "+ 계획 만들기") | -> SCR-PL-01 |
| SCR-PL-03 | Plan Detail | Screen (Deep View) | Administrator | SCR-PL-01 (Click vào thẻ Plan hoặc nút "보기") | -> SCR-PL-01, -> SCR-PL-04, -> SCR-PL-05 |
| SCR-PL-04 | Add Benefit Pop-up | Modal | Administrator | SCR-PL-02/03 (Nút "+" khối Benefits) | -> SCR-PL-03 |
| SCR-PL-05 | Amount Changes History | Drawer | Administrator | SCR-PL-03 (Link "View amount changes") | -> SCR-PL-03 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-PL-01 — Plan List
**Mục đích:** Hiển thị danh sách các gói bảo hiểm hiện có, phân loại theo loại gói và trạng thái. 
**Tác nhân chính:** Quản trị viên (Administrator)

#### A. Bố cục / Các thành phần
- **Header:** Tiêu đề "Plan" kèm badge hiển thị tổng số gói (ví dụ: `5`).
- **Toolbar:** 
  - Nút `+ 계획 만들기` (Tạo gói mới - Màu đen).
  - Checkbox lọc trạng thái `Plan Status`: [x] `Active`, [x] `Inactive`.
- **Content Area (Grid View):** Các gói được nhóm theo **Loại gói (Plan Type)**.
  - Ví dụ nhóm: "안전한 배송 계획" (Gói giao hàng an toàn), "일반 책임 보험 계획" (Gói bảo hiểm trách nhiệm chung).
  - **Plan Card:** 
    - Hình ảnh minh họa (Thumbnail).
    - Tên gói (Plan name), Trạng thái (Badge Active/Inactive).
    - Số lượng người dùng đã đăng ký (e.g., `12.3k registed`).
    - Mô tả ngắn (Description).
    - Giá (Amount): Hiển thị giá tháng và giá năm.
    - Nút `보기` (Xem chi tiết) xuất hiện khi hover.

---

### SCR-PL-02 & SCR-PL-03 — Create & Edit Plan Detail
**Mục đích:** Xem và chỉnh sửa thông tin chi tiết của một gói bảo hiểm, cấu hình giá và công ty bảo hiểm liên kết.
  
#### A. Thông tin chung (GENERAL)
- **Top Bar Actions (Edit mode):** Nút `Delete` (Đỏ), `Reset`, `Update` (Primary Black).
- **Banner Image:** Vùng upload/hiển thị ảnh đại diện cho gói bảo hiểm (Tỷ lệ 3:4, tối đa 150MB).
- **Trạng thái:** Badge status kèm dropdown thay đổi `Active`/`Inactive`.
- **Các trường dữ liệu:**
  - `Plan name`: Text input (Tối đa 20 ký tự).
  - `Short description`: Text input (Tối đa 40 ký tự).
  - `Plan Type`: Radio buttons chọn loại gói.
  - `Insurance company`: Nút `+` để thêm/chọn công ty bảo hiểm. Có thể chọn nhiều công ty (Multi-select).
  - `Exclusion`: Textarea nhập các trường hợp ngoại trừ (Tối đa 500 ký tự).

#### B. Quản lý lợi ích (BENEFITS)
- Khối hiển thị danh sách các quyền lợi đi kèm gói.
- Nút `+` mở SCR-PL-04 để thêm quyền lợi mới.
- Mỗi bản ghi lợi ích gồm: Icon, Tên lợi ích và Giá trị hạn mức (Limit).

#### C. Quản lý phí (AMOUNT)
- **Cấu hình Phí theo Công ty bảo hiểm:** Hiển thị danh sách các khối (Blocks) tương ứng với từng công ty bảo hiểm đã chọn ở phần General.
- **Các trường nhập liệu phí:**
  - `Insurance fee`: Phí bảo hiểm gốc.
  - `Membership fee`: Phí thành viên (đã bao gồm VAT 10% - hệ thống tự tính hoặc nhập tay).
  - `Price (yearly)`: Tổng phí năm (Tự động tính = Insurance fee + Membership fee).
  - `Price (monthly)`: Phí tháng (Tự động tính hoặc hiển thị tương ứng).
- **Price Management Logic:** 
  - Admin có thể cập nhật phí trực tiếp tại đây.
  - Link `View amount changes`: Mở lịch sử thay đổi giá (SCR-PL-05).

---

### SCR-PL-04 — Add Benefit Pop-up
- **Mục đích:** Thêm quyền lợi mới cho gói bảo hiểm.
- **Trường nhập liệu:** 
  - `Benefit title` (20 ký tự), `Description` (40 ký tự), `Limit` (20 ký tự).
  - `Icon`: Chọn/Thay đổi icon đại diện cho quyền lợi.
- **Nút bấm:** `취소` (Hủy), `생성` (Tạo mới).

---

### SCR-PL-05 — Amount Changes History (Save log CRUD price)
- **Mục đích:** Ghi nhận và theo dõi mọi thay đổi về phí bảo hiểm và phí thành viên của gói.
- **Giao diện:** Side Drawer trượt ra từ bên phải.
- **Nội dung:** 
  - Timeline hiển thị các mốc thời gian thay đổi (e.g., `3 mins ago`, `2026-01-02 06:22 AM`).
  - Chi tiết từng thay đổi theo từng Công ty bảo hiểm (Insurance fee, Membership fee, Total yearly/monthly).
  - Nút `Roll back`: Cho phép Admin khôi phục lại mức giá tại thời điểm cũ.

---

## 3) Developer Notes
- **Lưu Log giá:** Cần một bảng riêng `plan_price_logs` để lưu vết mọi hành động bấm nút "Update" nếu có sự thay đổi trong khối Amount.
- **Logic Tính toán:** Cần đảm bảo công thức tính `Price (yearly)` luôn đồng bộ trên UI khi admin thay đổi giá trị Phí bảo hiểm hoặc Phí thành viên.
- **Insurance Company Mapping:** CRUD nhanh công ty bảo hiểm tại mục Insurance companydropdown/popover nếu Admin có quyền tương ứng.
