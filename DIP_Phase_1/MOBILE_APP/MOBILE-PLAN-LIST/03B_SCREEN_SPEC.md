# Đặc tả màn hình — MOBILE-PLAN-001 — v1.0
**Cập nhật lần cuối:** 2026-04-02
**Liên quan:** Xem gói bảo hiểm (User Plan View)

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-PL-01 | Plan List | Screen / Tab | User (Driver) | Bottom Nav > 혜택 (Benefits) | -> SCR-PL-02 |
| SCR-PL-02 | Plan Detail | Screen / Full Page | User (Driver) | SCR-PL-01 (Click vào thẻ Plan) | -> SCR-PL-01 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-PL-01 — Plan List
**Mục đích:** Hiển thị các danh sách gói bảo hiểm được đề xuất và gói bảo hiểm của tôi. Toàn bộ thông tin gói được cấu hình từ module **Plan Management** của Admin.
**Tác nhân chính:** Người dùng
**Điều kiện truy cập:** Click vào tab "혜택" (Benefits) ở thanh điều hướng dưới cùng.

#### A. Layout / Sections
- **Top Bar:** Tiêu đề "보험" (Bảo hiểm).
- **Tab Switcher:** `추천` (Đề xuất) và `내 혜택` (Lợi ích của tôi).
- **Banner/Intro:** Một câu giới thiệu ngắn về lợi ích bảo hiểm.
- **Plan Cards (Dữ liệu từ API):**
  - Hiển thị danh sách thẻ gói bảo hiểm theo chiều dọc.
  - Mỗi thẻ bao gồm: Hình ảnh (Thumbnail), Tên gói, Mô tả ngắn.
  - Badge trạng thái nếu cần (ví dụ: "준비중" - Đang chuẩn bị).
  - List Header (ví dụ: "1년 케어 패키지" - Gói chăm cứu 1 năm) dẫn đến nhóm nội dung cụ thể.

---

### SCR-PL-02 — Plan Detail
**Mục đích:** Hiển thị thông tin chi tiết và đầy đủ nhất về một gói bảo hiểm để người dùng quyết định đăng ký.
**Tác nhân chính:** Người dùng (Driver)

#### A. Layout / Sections (Dữ liệu hoàn toàn từ API/Admin Config)
- **Header:** 
  - Nút Đóng (X).
  - Tiêu đề: "플랜 상세정보" (Thông tin chi tiết gói).
- **Plan Identity:**
  - Hình ảnh minh họa lớn (Banner).
  - Tên gói và mô tả ngắn (Tương ứng với `Plan name` và `Short description` bên Admin).
- **Price Block:**
  - Hiển thị tiêu đề cấu hình (ví dụ: `1년 케어 패키지`).
  - Giá tháng và giá năm: `월 기준 7,340 원 (연 88,000원)`. (Lấy từ khối **Amount** bên Admin).
- **혜택 구성 (Benefits Configuration):**
  - Hiển thị danh sách các thẻ quyền lợi.
  - Phụ trách hiển thị các trường: **Icon**, **Title** (`Benefit title`), **Description**, và **Limit** (Hạn mức).
- **제외 사항 (Exclusion):**
  - Hiển thị các trường hợp ngoại trừ bảo hiểm. (Lấy từ trường `Exclusion` bên Admin).
- **4단계로 청구하기 (Claim Steps):**
  - Khối tĩnh hướng dẫn 4 bước yêu cầu bồi thường (1. 접수, 2. 확인, 3. 산정, 4. 지급).
- **Links:** "환불 정책 확인하기" (Kiểm tra chính sách hoàn tiền). Khi nhấn vào thì mở trang xem chính sách hoàn tiền.
- **Footer Button:** Nút `가입하기` (Đăng ký). Khi nhấn vào thì mở trang đăng ký gói bảo hiểm (Plan registration).

#### B. Logic & Data Flow
- **Data Source:** Toàn bộ nội dung hiển thị (Trừ phần 4 bước tĩnh) đều phải được map 1-1 với các cấu hình trong module **Plan Management** của phía Admin.
- **Dynamic Content:** Danh sách lợi ích (Benefits) phải linh động theo số lượng record mà admin đã thêm vào gói đó.

---

## 3) Developer Notes
- **API Mapping:**
  - `Plan Name` -> mapped to admin's `Plan name`.
  - `Price` -> mapped to admin's calculated `Price (yearly/monthly)`.
  - `Benefits List` -> mapped to admin's `Benefits` array.
  - `Exclusion Text` -> mapped to admin's `Exclusion` textarea.
- **UI:** Đảm bảo hiển thị đúng icons bồi thường đã được admin chọn.
