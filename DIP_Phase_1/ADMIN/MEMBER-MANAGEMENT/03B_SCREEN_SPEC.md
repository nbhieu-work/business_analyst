# Đặc tả màn hình — MEMBER-MANAGEMENT-001 — v1.0
**Cập nhật lần cuối:** 2026-05-04
**Liên quan:** Quản lý tài khoản thành viên (Member Account Management)

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-MM-01 | Member List | Screen (List) | Administrator | Admin Sidebar > Members | -> SCR-MM-02 |
| SCR-MM-02 | Member Details | Side-panel Drawer | Administrator | SCR-MM-01 (Click vào Account ID) | -> SCR-MM-01 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-MM-01 — Member List (Danh sách thành viên)
**Mục đích:** Cung cấp cái nhìn tổng quan toàn bộ tài khoản thành viên trên hệ thống, hỗ trợ lọc, tìm kiếm và thay đổi trạng thái nhanh.
**Tác nhân chính:** Quản trị viên (Administrator)

#### A. Bố cục / Các thành phần
- **Header:** Tiêu đề "Members" kèm badge tổng số thành viên (vd: `24`).
- **Toolbar:**
  - Nút `Export to Excel` (Dropdown) — Xuất danh sách hiện tại ra file Excel.
  - Checkbox `Show only change request` — Lọc chỉ hiển thị các tài khoản đang có yêu cầu thay đổi thông tin.
  - Ô tìm kiếm `Search by name, email, or account ID...`.
- **Bộ lọc Tab:** `ALL`, `ACTIVE`, `INACTIVE`.
- **Bảng dữ liệu (Data Table):**

| Cột | Mô tả |
|---|---|
| No | Số thứ tự |
| Account ID | Hiển thị dạng link màu xanh; click để mở SCR-MM-02 |
| Name | Họ tên đầy đủ |
| Status | Badge `Active` (xanh) / `Inactive` (xám); click để đổi nhanh |
| Phone number | Số điện thoại |
| Email | Email tài khoản |
| Registration No | Số CMND/CCCD (7 số đầu hiển thị, còn lại ẩn `*`) |
| Vehicles | Hiển thị biển số xe đầu tiên. Nếu có nhiều hơn 1 xe: badge `+N` (vd: `2+`). Hover vào badge xổ ra tooltip danh sách tất cả xe |
| Company | Tên công ty liên kết (Affiliated Company) |
| Applied date | Ngày đăng ký tài khoản (YYYY-MM-DD) |

- **Phân trang (Pagination):** Rows per page: 30, điều hướng First/Prev/Next/Last. Hiển thị `1-10 of 23`.

#### B. Tương tác người dùng
- **Click Account ID:** Mở Side Drawer SCR-MM-02.
- **Click Status Badge:** Mở pop-over "Change status" để chuyển đổi nhanh `Active` ↔ `Inactive`, gồm nút `Cancel` và `Apply`.
- **Checkbox "Show only change request":** Lọc danh sách hiện chỉ các thành viên đang có pending change request.

---

### SCR-MM-02 — Member Details (Chi tiết thành viên)
**Mục đích:** Xem và chỉnh sửa đầy đủ thông tin của một thành viên, bao gồm thông tin cá nhân, hợp đồng bảo hiểm và lịch sử hoạt động.
**Tác nhân chính:** Quản trị viên (Administrator)
**Entry Condition:** Click vào Account ID trên SCR-MM-01.

#### A. Layout tổng quan
- **Component:** Side-panel Drawer trượt ra từ bên phải (overlay màn hình list).
- **Header:** Avatar + Tên thành viên + Badge trạng thái (Dropdown `Active`/`Inactive`) + Nút đóng (X) + Điều hướng `<` `>` để chuyển giữa các member.
- **Tab Navigation:** 3 tab chính — `Personal Information`, `Contracts`, `Others`.

---

#### B. Tab 1: Personal Information
**Mục đích:** Xem và chỉnh sửa thông tin cơ bản của thành viên.

**Các trường:**
| Trường | Loại | Ghi chú |
|---|---|---|
| Account ID | Text Input | Có thể chỉnh sửa |
| Name | Text Input | Có thể chỉnh sửa |
| Resident Registration No (7 digits) | Text Input (masked) | 7 số đầu + ẩn 6 số sau (`●●●●●●`) |
| Phone number | Text Input | Có thể chỉnh sửa |
| Email | Text Input | Có thể chỉnh sửa |
| Vehicles | Hiển thị danh sách thẻ biển số xe | Mỗi thẻ có nút ✏️ Edit và nút `+` thêm xe mới |
| Company | Dropdown chọn Affiliated Company | Có thể thay đổi |

**Footer Actions:**
- Nút `Reset` — Hoàn tác thay đổi chưa lưu.
- Nút `Update` — Lưu thay đổi vào database.

---

#### C. Tab 2: Contracts (Hợp đồng)
**Mục đích:** Xem danh sách toàn bộ hợp đồng bảo hiểm hiện có của thành viên.

**Hiển thị:**
- Dòng tóm tắt: `TOTAL: N` (tổng số hợp đồng).
- **Contract Card** cho mỗi hợp đồng, gồm:
  - Mã hợp đồng (Certificate No, vd: `DRIVING-2024-001`) + Badge trạng thái (`Active`/`Inactive`).
  - Loại bảo hiểm (Plan name, vd: `Driving care Insurance`).
  - Ngày hết hạn và số ngày còn lại (vd: `Expires: 2025-12-31 (13 days remain)`).
  - Nút `View` → Mở chi tiết hợp đồng (liên kết đến Certificate Management).

---

#### D. Tab 3: Others (Thông tin khác)
**Mục đích:** Xem tổng quan hoạt động và lịch sử đồng ý điều khoản của thành viên.

**Khối ACTIVITIES:**
| Thống kê | Mô tả |
|---|---|
| TOTAL REPORTS | Tổng số báo cáo tai nạn đã gửi |
| ACTIVE POLICIES | Số hợp đồng bảo hiểm đang còn hiệu lực |
| TOTAL CLAIMS | Tổng số tiền bồi thường đã nhận (vd: `$12,500`) |

**Khối AGREEMENT LOGS:**
- Dropdown lọc theo loại điều khoản (`All` hoặc từng loại cụ thể).
- Danh sách các phiên bản điều khoản theo từng nhóm (vd: 서비스 이용 약관, 주식회사 개인정보처리방침, 개인정보 제3자 동의...).
- Mỗi dòng ghi nhận:
  - Mã phiên bản (vd: `20260204`) + Ngày xuất bản (vd: `published on 2026-02-12 06:22`).
  - Trạng thái đồng ý: `AGREED` (xanh), `NOT AGREED` (đỏ), `NO RESPONSE` (xám).
  - Click vào mã phiên bản → Mở popup xem nội dung điều khoản đó.

---

## 3) Developer Notes
- **Masking:** Số CCCD/Resident Registration Number phải được mask từ phía server — chỉ trả về 7 số đầu; không được expose toàn bộ số.
- **Vehicles tooltip:** Khi hover vào badge `+N`, hiển thị tooltip danh sách đầy đủ biển số — sử dụng lazy load nếu có nhiều xe.
- **Change Request flag:** Cần field hoặc bảng riêng để đánh dấu member đang có `pending change request` để hỗ trợ tính năng filter ở SCR-MM-01.
- **Agreement Logs:** Dữ liệu AGREEMENT LOGS phải được join từ bảng T&C với bảng user agreement history.
- **Navigation `<` `>`:** Cho phép chuyển giữa các member mà không cần đóng Drawer.
