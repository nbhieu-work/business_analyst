# Screen Spec — NOTIFICATION-001 — v1.0
**Cập nhật lần cuối:** 2026-03-30
**Liên quan:** US-Notification Management, BR-Notification Setup

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-01 | Notification Type List | List | Administrator | Admin Sidebar > Notification management | -> SCR-02, -> SCR-03 |
| SCR-02 | Add Notification Type | Side-panel / Drawer | Administrator | SCR-01 (Nút "+ Add Types") | -> SCR-01 |
| SCR-03 | Notification Type Details | Side-panel / Drawer | Administrator | SCR-01 (Click vào dòng hoặc chữ Edit) | -> SCR-01 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-01 — Notification Type List
**Purpose (Mục đích):** Quản lý và xem toàn bộ danh sách các loại thông báo (Notification Types) đã cài đặt trên hệ thống.  
**Primary Actor:** Quản trị viên (Admin)
**Entry Conditions:** Truy cập vào menu Admin > Notification management.

#### A. Bố cục / Các thành phần (Chức năng)
- **Breadcrumb/Header:** Tiêu đề "Notification / Notification Type" kèm theo badge hiển thị tổng số lượng (ví dụ: `24`).
- **Toolbar:** Nút `+ Add Types` và trường nhập liệu (input field) `Search Notification Type`.
- **Filter Tabs:** Các tab lọc trạng thái `ALL`, `ON`, `OFF`.
- **Content Area:** Bảng dữ liệu (Data table) có phân trang (ví dụ: dropdown chọn Rows per page: 30, và các nút điều hướng phân trang).
  - **Columns (Các cột phụ trách):**
    - No (Số thứ tự)
    - Notification Type Name (Click vào tên để xem chi tiết, ví dụ: "Change status")
    - Trigger (Event key, ví dụ: `CHANGE_STT`)
    - Description (Mô tả)
    - Status (Badge hiển thị trạng thái: `ON`/`OFF` kèm theo icon dropdown để chuyển đổi (toggle) trạng thái nhanh)
    - Create at (Định dạng YYYY-MM-DD)
    - Update at (Định dạng YYYY-MM-DD)
    - Action (Chứa text link `Edit`)

#### B. User Interactions (Tương tác người dùng)
- **Search:** Tìm kiếm tức thì (instant search) hoặc nhấn Enter để lọc danh sách theo `Type Name` hoặc `Trigger key`.
- **Filter Tabs:** Click vào tab để lọc các dòng trong bảng theo trạng thái bảng ON hoặc OFF.
- **Click row / "Edit" / Name:** Mở Drawer xem chi tiết phía bên phải màn hình (SCR-03).
- **Click "+ Add Types":** Mở Modal thêm mới (SCR-02).

---

### SCR-02 — Add Notification Type / SCR-03 — Edit Notification Type
**Purpose (Mục đích):** Tạo mới (Add) hoặc Chỉnh sửa (Edit) một loại thông báo. Cấu trúc màn hình Tạo và Sửa dùng chung một bộ Layout chia làm 3 Tabs.
**Primary Actor:** Quản trị viên (Admin)
**Entry Conditions:** 
- Tạo mới: Click nút "+ Add Types" từ màn hình danh sách (SCR-01).
- Chỉnh sửa: Click vào tên bài hoặc một dòng thuộc bảng danh sách (SCR-01).

#### A. Layout tổng quan
- **Component:** 
  - (Cả SCR-02 Add và SCR-03 Edit): Đều là **Side-panel Drawer** trượt ra từ bên phải màn hình.
- **Top Header:** Tiêu đề "Add Notification Type" hoặc "Notification Type Details" + icon Đóng (X).
- **Trạng thái Edit (SCR-03):** Có thêm Status Badge (Bật/tắt `ON`/`OFF`), nút `Send Test`, và Footer có nút `Delete`, `Update`, `Reset`.
- **Tabbed Navigation (Điều hướng qua Tab):**
  - `General` (Chứa phần Info và Template)
  - `Schedule` 
  - `Sending Rules` (Chỉ cần làm UI)

---

#### B. Tab: General (Chứa Info & Template)
**Purpose (Mục đích):** Cấu hình các thông tin nền tảng, thiết lập trigger và soạn thảo nội dung hiển thị của thông báo.

**Khối 1: NOTIFICATION INFO (Thông tin cơ bản)**
- **Notification Type Name:** Text input. _Placeholder: "e.g., Payment Completed"_
  - *Purpose (Mục đích):* Tên định danh của loại thông báo.
- **Description:** Textarea. _Placeholder: "Brief description (optional - for internal admin)"_ (0 / 500 ký tự).
  - *Purpose (Mục đích):* Ghi chú nội bộ cho nội bộ Admin hiểu rõ ngữ cảnh gửi.
- **Event-driven:** Switch Toggle (`ON`/`OFF`). 
  - *Purpose (Mục đích):* Nếu `ON` là thông báo hệ thống tự động bắn khi có event. Nếu `OFF` là thông báo gửi hàng loạt thủ công (Broadcast/Campaign).
  - **Rule:** Nếu bật `ON` -> Bắt buộc chọn **Trigger/Event Key**.
- **Trigger/Event Key:** Dropdown chọn mã trigger. _Ví dụ: "Accident Submission"_.
  - *Purpose (Mục đích):* Khóa API map với backend.
- **Sub-status Checkboxes (Hiển thị linh động theo Trigger):** Ví dụ như `Received`, `Under Review`, `Approved`, `Rejected`. Cho phép cấu hình gửi thông báo tại một trạng thái cụ thể của sự kiện.

**Khối 2: TEMPLATE (Thiết kế nội dung)**
- **Noti title:** Text input. _Placeholder: "Enter notification title (optional)"_
  - *Purpose (Mục đích):* Tiêu đề chính của thông báo (Required, trim spaces, max length 80).
- **Noti body:** Textarea. _Placeholder: "Use '/' for variable suggestion"_ (0 / 1,000 ký tự).
  - *Purpose (Mục đích):* Nội dung thân của thông báo. Khuyến khích sử dụng các biến động (dynamic variables).
- **Available Variables (Các biến có sẵn):** Khối hiển thị thông tin dạng read-only. Các thẻ tags (ví dụ: `{{username}} - User name`, `{{date}} - Date`) đi kèm icon Copy.
- **Number of button:** Radio buttons (`2`, `1`, `No button`).
  - *Purpose (Mục đích):* Cấu hình số lượng hộp thoại Call-to-action (CTA).
- **Button setting (Chỉ hiển thị nếu Số lượng nút > 0):** Khối nút vật lý đại diện (ví dụ: `Button 1`, `Button 2`). Khi click vào mỗi nút sẽ mở ra một Pop-over cài đặt:
  - **Button label:** Text input (Tối đa 8 ký tự).
  - **Type:** Radio buttons (`Web link`, `App deep link`).
  - **URL / Deep link:** Text input.
  - **Pop-over Footer:** `Cancel`, `Apply`.

**Footer của Tab General:**
- **Add Mode:** Nút `Cancel`, Nút `Save`.
- **Edit Mode:** Nút `Delete` (Mở modal xác nhận xoá), Nút `Reset` (Mặc định disable), Nút `Update` (Chỉ enable khi nội dung bị thay đổi).

---

---

#### C. Tab: Schedule
**Purpose (Mục đích):** Xác định *khi nào (when)* thông báo sẽ được kích hoạt và gửi đi.

- **SELECT SENDING OPTION (Các thẻ cấu hình Radio):**
  1. **Send immediately:** Gửi ngay lập tức khi sự kiện được kích hoạt (trigger).
  2. **Recurring schedule:** Gửi lặp lại theo một lịch trình định kỳ.

**Dynamic SCHEDULE SETTING sections (Khối giao diện biến đổi phụ thuộc vào tùy chọn ở trên):**
- **Nếu chọn `Send immediately`:**
  - Thông báo sẽ được đẩy đi ngay mà không có cấu hình độ trễ (delay) nào.
- **Nếu chọn `Recurring schedule`:**
  - **Frequency:** Radio buttons (`Daily`, `Weekly`, `Monthly`).
  - **Send time:** Input nhập khung giờ (HH:MM).
  - **Days of week:** Dropdown lấy nhiều giá trị. Có 3 options nhanh trong dropdown popover: `All days (7 days/week)`, `Weekdays only (Mon-Fri)`, `Custom selection`.
    - *UI Logic Note:* Các tuỳ chọn trong Dropdown và bảng Toggle Mon->Sun bên dưới liên kết chặt chẽ (2 chiều). Nếu Admin click chọn Mon-Fri ở dưới, tuỳ chọn trên dropdown tự "update tương ứng" thành Weekdays only.
  - **Start date:** Công cụ Date picker.
  - **End date (optional):** Công cụ Date picker.
- **Footer Action:** Nút `Schedule Notification` (hoặc `Update`).

---

#### D. Tab: Sending Rules (CHỈ CẦN LÀM UI, CHƯA CẦN LÀM BACKEND)
**Purpose (Mục đích):** Hạn chế hoặc chọn lọc *ai (who)* sẽ nhận thông báo và *mức độ lặp lại (how often)* để chống spam. Nhằm mục đích bảo vệ trải nghiệm người dùng cuối và tối ưu chi phí gửi tin cho doanh nghiệp.

- **ELIGIBILITY (Define who can receive this notification):**
  - *Purpose (Mục đích):* Xác định tập đối tượng được phép nhận thông báo, giúp gửi đúng người, tránh lãng phí.
  - **Target Audience:** Radio buttons -> `All users` vs `Specific Users`.
    - *Dependency 1:* Nếu chọn `Specific Users`, hiển thị một vùng tải lên chứa nút "Select file" (Chỉ chấp nhận file `.xlsx` hoặc `.xls`. Dung lượng tải tối đa 100MB).
    - *Dependency 2 (CRITICAL - QUAN TRỌNG):* Toàn bộ phần Target Audience này (bao gồm cả chỗ Upload Excel) **CHỈ ĐƯỢC PHÉP HIỂN THỊ nếu biến Notification Category == `Broadcast (Campaign)`**. Nếu loại thông báo là `Event-driven`, bắt buộc phải ẨN đi (vì user mục tiêu sẽ được code backend tự xác định qua payload thông số của sự kiện).
  - **Only active users:** Switch Toggle chuyển đổi Bật/Tắt (ON/OFF).
    - *Purpose (Mục đích):* Bộ lọc tự động loại bỏ các tài khoản "ma" hoặc đã gỡ app quá lâu, giúp tiết kiệm triệt để ngân sách SMS/Email (do gửi cho những người này cũng không mang lại chuyển đổi).
    - *Hint:* "Users who haven't logged in for 30+ days will not receive this notification".

- **DEDUPLICATION (Prevent duplicate notifications within a time window):**
  - *Purpose (Mục đích):* Ngăn chặn lỗi sự cố phần mềm hoặc do thao tác cố ý của user (ví dụ: bấm nút Mua Hàng 2 lần liên tục) gây ra hệ quả gửi ra 2 tin nhắn y hệt nhau tới máy người dùng.
  - **Toggle Switch:** ON/OFF.
  - **Deduplication strategy (Chỉ hiển thị nếu bật ON):** Radio options -> `By user + notification type` vs `By user + trigger reference`.

- **RATE LIMITING (Control notification frequency per user):**
  - *Purpose (Mục đích):* Kiểm soát và giới hạn số lượng tin thông báo tối đa mà 1 cá nhân phải chịu để tránh làm phiền họ liên tục (anti-spam), qua đó duy trì điểm uy tín cao (credibility score) cho hệ thống bắn tin.
  - **Toggle Switch:** ON/OFF.
  - **Max per day (Chỉ hiển thị nếu bật ON):** Input block (Hint/Gợi ý: "Per user, per 24 hours").
  - **Max per week (Chỉ hiển thị nếu bật ON):** Input block (Hint/Gợi ý: "Per user, per 7 days").

- **RETRY & FAILURE HANDLING (Automatic retry for failed deliveries):**
  - *Purpose (Mục đích):* Bảo đảm các loại thông báo quan trọng (ví dụ: tin nhắn mã OTP, Hoá đơn thanh toán) chắc chắn phải tới được tận tay user trong tình huống đối tác gửi tin (SMS/Email provider) gặp gián đoạn tạm thời.
  - **Toggle Switch:** ON/OFF.
  - **Maximum retries (Chỉ hiển thị nếu bật ON):** Counter component (hộp đếm số) có phím trừ/cộng `-` [number] `+`.
  - **Max retry window (hours) (Chỉ hiển thị nếu bật ON):** Biểu mẫu Input dạng số Number block (ví dụ: `24`).

- **Footer Action:** Nút Button lưu chạy đồng bộ Save (`Save Rules` hoặc `Update`).

---

### SCR-04 — Send Test Popup
**Purpose (Mục đích):** Gửi thử một thông báo đến thiết bị để kiểm tra hiển thị thực tế của Template trước khi áp dụng cho người dùng cuối. 
**Primary Actor:** Quản trị viên (Admin)
**Entry Conditions:** Click nút `Send Test` góc phải trên màn hình SCR-02 hoặc SCR-03.

#### A. Bố cục / Các thành phần
- **Component:** Pop-up Modal hiển thị chính giữa.
- **Header:** Tiêu đề "Send Test" + icon Đóng (X).
- **Form Fields (Các trường tương tác):**
  - **AccountID:** Text input. Nơi chỉ định account nhận test.
    - *Hint text:* "If entering multiple items, separate them with a comma (,)". Cho phép gửi test tới nhiều người cùng lúc.
  - **Checkbox "Use current draft (not published)":** Checkbox kiểm chọn.
    - *Purpose:* Nếu bấm chọn, hệ thống sẽ gửi đi nội dung admin VỪA GÕ trên tab General (thậm chí chưa bấm Update).
    - *Hint text:* "If not checked, the system will send according to the saved template" (Nếu không check, hệ thống sẽ lấy template đã lưu database ra gửi).
- **Footer Action:** Nút `Cancel`, Nút `Send`.

---

### Màn hình xác nhận Delete popup
- Bật lên khi Admin ấn nút `Delete` đỏ ở dưới cùng tab General (chế độ Edit).
- Hiển thị cảnh báo: tên (Notification Type), trigger và câu hỏi "Are you sure you want to delete this notification type? This action cannot be undone."
- Các nút: `Cancel` và `Delete`.
