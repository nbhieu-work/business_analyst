# Screen Spec — NOTIFICATION-001 — v1.0
**Cập nhật lần cuối:** 2026-03-30
**Liên quan:** US-Notification Management, BR-Notification Setup

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-01 | Notification Type List | List | Administrator | Admin Sidebar > Notification management | -> SCR-02, -> SCR-03 |
| SCR-02 | Add Notification Type | Modal | Administrator | SCR-01 (Nút "+ Add Types") | -> SCR-01 |
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

### SCR-02 — Add Notification Type
**Purpose (Mục đích):** Tạo một loại thông báo mới bao gồm các thông tin nền tảng (Tên, Mã kích hoạt, Phân loại).  
**Primary Actor:** Quản trị viên (Admin)

#### A. Bố cục / Các thành phần (Chức năng)
- **Component:** Pop-up Modal hiển thị chính giữa màn hình.
- **Header:** Tiêu đề "Add Notification Type" + icon Đóng (X).
- **Form Fields (Các trường nhập liệu):**
  - **Type Name:** Text input. _Placeholder: "e.g., Payment Completed"_
    - *Purpose (Mục đích):* Tên định danh của loại thông báo, dùng để Admin dễ dàng quản lý và tìm kiếm trên giao diện.
  - **Notification Category:** Toggle bật/tắt tuỳ chọn: `Event-driven (System)`.
    - *Purpose (Mục đích):* Phân loại luồng hoạt động. `Event-driven` để phản hồi tự động lại các thao tác của user trên app. Khi tắt dùng cho mục đích CSKH/Marketing gửi hàng loạt theo chiến dịch.
    - **Rule:** Nếu bật `Event-driven` -> Hiển thị trường **Trigger/Event Key**.
    - **Rule:** Nếu tắt `Event-driven` -> Ẩn trường **Trigger/Event Key** (vì loại này sẽ được chạy bằng lịch hẹn (scheduling), không chạy bằng mã code).
  - **Trigger/Event Key:** Dropdown (Dữ liệu được lấy qua Backend API, ví dụ: `/api/notification-triggers`). _Placeholder: "Select a trigger..."_
    - *Purpose (Mục đích):* Mã khoá kết nối giữa System Code và Hệ thống Thông báo. Khi code phía backend gọi mã này, hệ thống sẽ biết cần bốc đúng template này ra gửi.
    - **Reasoning:** Do các trigger được code cứng chìm trong logic backend, trường này BẮT BUỘC phải là dạng danh sách sổ xuống (Dropdown) để tránh Admin gõ sai chính tả (typo) và giúp Admin nhận biết được hệ thống đang hỗ trợ những event nào.
  - **Description:** Textarea. _Placeholder: "Brief description (optional - for internal admin)"_
    - *Purpose (Mục đích):* Ghi chú nội bộ cho các Admin khác hiểu rõ hơn về ngữ cảnh sử dụng của loại thông báo này.
- **Footer Buttons:** Nút `Cancel`, Nút `Create`.

---

### SCR-03 — Notification Type Details
**Purpose (Mục đích):** Cấu hình toàn diện cho một loại thông báo bao gồm lịch trình gửi đi, luật chống trùng lặp dữ liệu, tập đối tượng và cấu hình chi tiết (templates).  
**Primary Actor:** Quản trị viên (Admin)
**Entry Conditions:** Click vào một dòng trong bảng danh sách tại SCR-01.

#### A. Layout (Persistent Drawer Shell)
- **Component:** Drawer trượt ra từ bên phải màn hình.
- **Top Bar:** Icon Đóng (X), Tiêu đề "Notification Type Details", Các nút mũi tên chuyển qua lại giữa các record (`<` `>`).
- **Main Header Profile:**
  - Tên loại thông báo (Ví dụ: "Change status").
  - Status Badge: Trạng thái `ON`/`OFF` kèm dropdown để thay đổi nhanh trạng thái bật/tắt.
  - Các nút hành động: Nút `Send Test`, Nút `Edit`.
- **Tabbed Navigation (Điều hướng qua Tab):**
  - `Template`
  - `Schedule` 
  - `Sending Rules` 

---

#### B. Tab: Template
**Purpose (Mục đích):** Soạn thảo và cấu hình thiết kế nội dung hiển thị của thông báo gửi tới người dùng cuối.

- **Message title:** Text input. _Placeholder: "Enter message title (optional)"_
  - *Purpose (Mục đích):* Tiêu đề chính của thông báo (Push/Email title). Có thể để trống nếu kênh gửi không yêu cầu tiêu đề (như SMS cũ).
- **Message body:** Textarea. _Placeholder: "Use variables like {{username}}, {{planName}}, {{amount}}..."_ (Tối đa 1,000 ký tự).
  - *Purpose (Mục đích):* Nội dung thân của thông báo. Khuyến khích sử dụng các biến động (dynamic variables) để cá nhân hoá thông tin.
- **Available Variables (Các biến có sẵn):** Khối hiển thị thông tin dạng read-only.
  - *Purpose (Mục đích):* Liệt kê các danh sách tag biến động mà Admin được phép dùng cho loại thông báo này (dữ liệu truyền từ Backend sang).
  - *UI Component:* Các thẻ tags (ví dụ: `{{username}} - User name`, `{{date}} - Date`) đi kèm icon Copy để Admin bấm vào sao chép nhanh vào vùng soạn thảo.
- **Number of button:** Radio buttons (`2`, `1`, `No button`).
  - *Purpose (Mục đích):* Cấu hình số lượng nút bấm Call-to-action (CTA) đính kèm dưới cùng của bảng thông báo (thường dùng cho Kakao Alimtalk hoặc In-app Message).
- **Button setting (Chỉ hiển thị nếu Số lượng nút > 0):** 
  - *Purpose (Mục đích):* Khối nút vật lý đại diện (ví dụ: `Button 1`, `Button 2`). Khi click vào mỗi nút sẽ mở ra một Pop-over cài đặt:
    - **Button label:** Text input (Tối đa 8 ký tự). _Placeholder: "Button label"_. Tên nút hiển thị.
    - **Type:** Radio buttons (`Web link`, `App deep link`). Phân loại hành động khi user ấn nút.
    - **URL / Deep link:** Text input. _Placeholder: "Input Link"_. Link đích điều hướng user.
    - **Pop-over Footer:** `Cancel`, `Apply`.
- **Footer Action:** Nút `Reset` để huỷ thay đổi, và Nút `Save template` để lưu lại.

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
  - **Days of week:** Dropdown hỗ trợ lấy nhiều giá trị (multi-select) (ví dụ: "Custom selection") bên dưới là các nút Toggles cho từng ngày (`Mon`, `Tue` (ẩn đi), `Wed`, v.v.) để tuỳ chỉnh quy luật lặp lại.
  - **Start date:** Công cụ Date picker.
  - **End date (optional):** Công cụ Date picker.
- **Footer Action:** Nút `Schedule Notification`.

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

- **Footer Action:** Nút Button lưu `Save Rules`.
