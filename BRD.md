# Business Requirements Document (BRD)
# Dự án: Insurance Super App (DIP)
**Phiên bản:** 1.0  
**Ngày tạo:** 2026-05-04  
**Trạng thái:** Draft  

---

## 1. Tổng quan dự án (Project Overview)

### 1.1 Mục tiêu
Xây dựng một nền tảng bảo hiểm tích hợp (Insurance App) phục vụ thị trường Hàn Quốc, bao gồm:
- **Admin Dashboard:** Hệ thống quản trị nội bộ cho phép quản lý gói bảo hiểm, công ty liên kết, báo cáo tai nạn và thông báo.
- **Mobile App:** Ứng dụng di động cho người dùng cuối (tài xế giao hàng) để xem gói bảo hiểm, báo cáo tai nạn và nhận thông báo.

### 1.2 Đối tượng người dùng
| Vai trò | Mô tả |
|---|---|
| Administrator | Nhân viên nội bộ vận hành hệ thống Admin Dashboard |
| Driver (User) | Tài xế giao hàng sử dụng Mobile App |
| Affiliated Company | Doanh nghiệp đối tác liên kết với nền tảng |

### 1.3 Phạm vi (Scope)
- **In Scope:** Admin modules (Notification, Plan Management, Accident Management, Plan Subscription, Affiliated Company, T&C) và Mobile App (Home, Plan View, Notification List).
- **Out of Scope:** Module Community (ON HOLD), Payment Gateway tự động.

---

## 2. Các Module và Yêu cầu Nghiệp vụ

### 2.1 [ADMIN] Quản lý Thông báo (Notification Management)

**Mục tiêu:** Cho phép Admin cấu hình, lên lịch và quản lý toàn bộ vòng đời của các loại thông báo gửi đến người dùng.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| NOTi-01 | Xem danh sách tất cả Notification Types với các cột: Tên, Trigger key, Mô tả, Trạng thái ON/OFF | Must Have |
| NOTI-02 | Tạo mới / Chỉnh sửa Notification Type qua Side Drawer với 3 tab: General, Schedule, Sending Rules | Must Have |
| NOTI-03 | Cấu hình Template thông báo với hỗ trợ Dynamic Variables (`{{username}}`, `{{date}}`) | Must Have |
| NOTI-04 | Phân loại loại thông báo: Event-driven (tự động khi có event) hoặc Broadcast (gửi hàng loạt thủ công) | Must Have |
| NOTI-05 | Cấu hình lịch gửi: Send Immediately hoặc Recurring Schedule (Daily/Weekly/Monthly) | Must Have |
| NOTI-06 | Cấu hình Sending Rules: Eligibility (All/Specific Users), Deduplication, Rate Limiting, Retry | Should Have |
| NOTI-07 | Tính năng Send Test gửi thông báo mẫu đến Account ID chỉ định | Should Have |
| NOTI-08 | Toggle bật/tắt Notification Type nhanh từ danh sách | Must Have |

**Quy tắc nghiệp vụ:**
- Khi `Event-driven = ON`, bắt buộc phải chọn Trigger/Event Key.
- Phần Target Audience trong Sending Rules chỉ hiển thị khi Notification Category là `Broadcast`. Nếu là `Event-driven`, phải ẩn phần này đi.
- Nội dung Title và Body của thông báo hiển thị trên Mobile App được lấy trực tiếp từ template đã cấu hình ở đây.

---

### 2.2 [ADMIN] Quản lý Công ty Liên kết (Affiliated Company Management)

**Mục tiêu:** Quản lý danh mục các đối tác doanh nghiệp liên kết với nền tảng.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| AFF-01 | Xem danh sách công ty với bộ lọc tab: ALL / ACTIVE / INACTIVE | Must Have |
| AFF-02 | Tạo công ty mới với các trường: Tên công ty, Mã số đăng ký kinh doanh | Must Have |
| AFF-03 | Chỉnh sửa thông tin công ty (Tên, Mã đăng ký) | Must Have |
| AFF-04 | Chuyển đổi trạng thái Active/Inactive cho từng công ty mà không xóa dữ liệu | Must Have |

**Quy tắc nghiệp vụ:**
- Công ty **không được xóa vĩnh viễn** để bảo toàn dữ liệu lịch sử (người dùng, phương tiện đã gắn với công ty).
- Khi chuyển sang `Inactive`, công ty vẫn hiển thị trong Admin nhưng bị ẩn khỏi danh sách lựa chọn công khai cho người dùng mới.
- Format Mã đăng ký kinh doanh: `XXX-XX-XXXXX`.

---

### 2.3 [ADMIN] Quản lý Tai nạn (Accident Management)

**Mục tiêu:** Quản lý và cập nhật kết quả xử lý các hồ sơ báo cáo tai nạn từ phía người dùng.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| ACC-01 | Xem danh sách báo cáo tai nạn với bộ lọc: ALL / RECEIVED / UNDER REVIEW / APPROVED / REJECTED | Must Have |
| ACC-02 | Thay đổi nhanh trạng thái hồ sơ bằng cách click vào badge Status | Must Have |
| ACC-03 | Import hàng loạt kết quả xử lý tai nạn từ file Excel (tối đa 5 files/lần, mỗi file tối đa 100MB) | Must Have |
| ACC-04 | Cung cấp file template mẫu để Admin tải về | Must Have |

**Cấu trúc file Excel Import:**
`certificate no.` | `vehicle no.` | `status` | `claim manager` | `claim manager contact` | `loss assessment company` | `loss assessment manager` | `loss assessment manager contact` | `claim amount` | `reason for rejection`

**Quy tắc nghiệp vụ (Logic xử lý Import):**
- **Key mapping:** Sử dụng cặp `certificate no.` + `vehicle no.` để xác định hồ sơ.
- **Skip rule:** Bỏ qua dòng nếu thiếu bất kỳ trường nào trong bộ 3: `certificate no.`, `vehicle no.`, `status`.
- **Xử lý trùng:** Nếu một cặp xe-chứng chỉ có nhiều hồ sơ tai nạn, chỉ cập nhật hồ sơ **MỚI NHẤT** (dựa trên Report Date).
- **Phản hồi:** Hiển thị số lượng Success/Failed sau khi Import hoàn tất.

---

### 2.4 [ADMIN] Quản lý Gói Bảo hiểm (Plan Management)

**Mục tiêu:** Tạo và quản lý các gói bảo hiểm, bao gồm cấu hình giá, quyền lợi và công ty bảo hiểm.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| PLN-01 | Xem danh sách gói bảo hiểm dạng Grid, phân nhóm theo Plan Type | Must Have |
| PLN-02 | Tạo mới / Chỉnh sửa gói bảo hiểm với đầy đủ thông tin: Tên, Mô tả, Plan Type, Công ty BH, Exclusion | Must Have |
| PLN-03 | Quản lý Quyền lợi (Benefits): thêm/sửa/xóa với các trường Icon, Tiêu đề, Mô tả, Hạn mức | Must Have |
| PLN-04 | Cấu hình Phí (Amount) theo từng Công ty bảo hiểm: Insurance fee, Membership fee (VAT 10%), giá Năm/Tháng tự động tính | Must Have |
| PLN-05 | Xem lịch sử thay đổi giá (Price Audit Log) với khả năng Rollback về mức giá cũ | Must Have |
| PLN-06 | Chuyển đổi trạng thái Active/Inactive cho gói bảo hiểm | Must Have |

**Quy tắc nghiệp vụ:**
- `Price (yearly)` = `Insurance fee` + `Membership fee` (tự động tính khi admin thay đổi một trong hai).
- Mọi thay đổi về phí phải được ghi nhận vào bảng `plan_price_logs` (Old value, New value, Updated by, Timestamp).
- Chức năng Rollback giá phải khôi phục đồng thời cả `Insurance fee` và `Membership fee`.

---

### 2.5 [ADMIN] Quản lý Thành viên (Member Management)

**Mục tiêu:** Cung cấp công cụ để Admin xem, tìm kiếm, quản lý trạng thái và chỉnh sửa thông tin toàn bộ tài khoản thành viên trên nền tảng.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| MEM-01 | Xem danh sách thành viên với tab lọc: ALL / ACTIVE / INACTIVE | Must Have |
| MEM-02 | Tìm kiếm thành viên theo: Tên, Email, Account ID | Must Have |
| MEM-03 | Lọc riêng các tài khoản đang có "Change Request" pending | Should Have |
| MEM-04 | Thay đổi nhanh trạng thái Active/Inactive từ danh sách (click vào badge Status) | Must Have |
| MEM-05 | Xem chi tiết thành viên qua Side Drawer với 3 tab: Personal Info, Contracts, Others | Must Have |
| MEM-06 | Chỉnh sửa thông tin cá nhân: Account ID, Tên, SĐT, Email, Công ty liên kết | Must Have |
| MEM-07 | Xem danh sách xe của thành viên trong Drawer | Must Have |
| MEM-08 | Xem danh sách hợp đồng bảo hiểm đang active của thành viên | Must Have |
| MEM-09 | Xem thống kê hoạt động: Total Reports, Active Policies, Total Claims | Must Have |
| MEM-10 | Xem lịch sử đồng ý điều khoản (Agreement Logs) theo từng phiên bản T&C | Must Have |
| MEM-11 | Export danh sách thành viên ra Excel | Should Have |

**Quy tắc nghiệp vụ:**
- **Số CCCD/Resident Registration No:** Chỉ hiển thị 7 số đầu, phần còn lại phải được **mask** (`●●●●●●`) — không được expose toàn bộ số.
- **Vehicles:** Hiển thị biển số đầu tiên; nếu có nhiều xe, hiển thị badge `+N` và tooltip danh sách khi hover.
- **Contract View:** Tab Contracts trong Drawer liên kết đến dữ liệu từ module Certificate Management.
- **Agreement Logs:** Lịch sử đồng ý phân nhóm theo từng loại điều khoản với 3 trạng thái: `AGREED` / `NOT AGREED` / `NO RESPONSE`.

---

### 2.6 [ADMIN] Quản lý Chứng chỉ Bảo hiểm (Certificate Management)

**Mục tiêu:** Quản lý toàn bộ vòng đời của các chứng chỉ bảo hiểm, bao gồm tạo mới, upload danh sách người dùng hàng loạt, gắn file PDF và theo dõi lịch sử gia hạn.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| CERT-01 | Xem danh sách chứng chỉ với bộ lọc tab: ALL / ACTIVE / INACTIVE | Must Have |
| CERT-02 | Tạo chứng chỉ mới: chọn Plan, nhập Certificate No, Member Type (Corporate/Individual), ngày hiệu lực | Must Have |
| CERT-03 | Upload danh sách người dùng hàng loạt qua file Excel (chỉ áp dụng cho Member Type = Corporate) | Must Have |
| CERT-04 | Upload file PDF chứng chỉ chính thức (Bắt buộc nếu Status = Active; Tùy chọn nếu Inactive) | Must Have |
| CERT-05 | Xem chi tiết chứng chỉ qua Side Drawer với 3 tab: General Info, Certificate File, Renewal History | Must Have |
| CERT-06 | Chỉnh sửa và cập nhật thông tin chứng chỉ | Must Have |
| CERT-07 | Export danh sách chứng chỉ ra Excel | Should Have |

**Quy tắc nghiệp vụ (Bulk Mapping Flow):**
- Chứng chỉ chỉ được chuyển sang trạng thái `Active` khi: (1) File PDF đã được upload, và (2) Ngày hiện tại nằm trong khoảng Start Date – End Date hợp lệ.
- **Quy tắc 1 Plan/xe:** Một phương tiện có thể có nhiều chứng chỉ khác loại gói, nhưng chỉ được có **DUY NHẤT** một chứng chỉ cho mỗi Plan Type.
- Khi upload Excel: Nếu user đã tồn tại → dùng User ID hiện có. Nếu chưa có → tự động tạo tài khoản mới từ dữ liệu Excel.
- Field `Corporate Name` bị ẩn khi Member Type = Individual.
- Field Upload Excel bị ẩn khi Member Type = Individual.

---

### 2.6 [ADMIN] Quản lý Đăng ký Gói (Plan Subscription)

**Mục tiêu:** Quản lý danh sách người dùng đã đăng ký gói bảo hiểm, bao gồm gán chứng chỉ và xử lý các thay đổi hàng loạt.

#### 2.5.1 Chức năng Gán Chứng chỉ (Assign Certificate)

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| SUB-01 | Chọn nhiều đơn đăng ký (tối đa 30) để thực hiện gán chứng chỉ | Must Have |
| SUB-02 | Disable checkbox với đơn đã có Certificate (không cho gán đè) | Must Have |
| SUB-03 | Tìm kiếm Certificate theo: số, công ty, gói bảo hiểm | Must Have |
| SUB-04 | Hệ thống tự động validate: báo `Invalid` nếu Plan của Certificate không khớp với Plan của đơn đăng ký | Must Have |
| SUB-05 | Cho phép gán đồng loạt các đơn `Valid` sau khi kiểm tra | Must Have |

**Lý do Invalid hiện tại:** "The insurance plan don't match." (Kế hoạch bảo hiểm không khớp).

#### 2.5.2 Chức năng Thay đổi Biển số xe Hàng loạt (Vehicle Number Update Bulk)

**Format Excel:** `Old vehicle number` | `Name` | `DOB` | `Phone` | `New vehicle number`

**Logic kiểm tra:**
- B1. Validate format: Không field nào null, DOB/Phone/Vehicle number đúng định dạng Regex.
- B2. Check tồn tại: `Old vehicle number` phải có trong hệ thống.
- B3. Verify User: Kiểm tra và so khớp (Name, DOB, Phone, Affiliated Company) với xe cũ.
- B4. Check xe mới: Không được tồn tại sẵn, không được trùng xe cũ.
- B5. Thực hiện Update đồng thời 3 bảng: `Vehicle`, `User` (nếu có), `Plan Subscription`.
- B6. Ghi Audit Log: Old plate, New plate, Updated by, Date, Time.

#### 2.5.3 Chức năng Nghỉ việc Hàng loạt (Offboard Bulk)

**Format Excel:** `Vehicle number` | `Name` | `DOB` | `Phone`

**Logic kiểm tra:**
- B1. Validate format: Không field nào null, DOB/Phone hợp lệ.
- B2. Verify User: So khớp 5 trường (Vehicle number, Name, DOB, Phone, Affiliated Company).
- B3. Soft Delete: Điền `delete_at` trong database cho record Plan Subscription liên quan.

---

### 2.8 [ADMIN] Quản lý Điều khoản (Terms & Conditions Management)

**Mục tiêu:** Quản lý vòng đời các phiên bản điều khoản, lên lịch xuất bản và theo dõi lượt đồng ý.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| TNC-01 | Xem danh sách T&C với lọc trạng thái: ALL / PUBLISHED / SCHEDULED / DRAFT | Must Have |
| TNC-02 | Soạn thảo nội dung T&C bằng Rich Text Editor (WYSIWYG) | Must Have |
| TNC-03 | Cấu hình: Tên điều khoản, Trang hiển thị, Loại (Bắt buộc / Tùy chọn / Đọc) | Must Have |
| TNC-04 | Lên lịch xuất bản (Scheduled) hoặc xuất bản ngay lập tức | Must Have |
| TNC-05 | Xem lịch sử phiên bản theo dạng Timeline | Must Have |
| TNC-06 | Xem danh sách người dùng đã đồng ý từng phiên bản (Agreed Users) | Must Have |

**Vòng đời trạng thái:** `Draft` → `Scheduled` → `Published`

---

### 2.9 [MOBILE APP] Trang chủ & Điều khoản (Home & T&C)

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| MOB-01 | Bắt buộc đồng ý T&C mới nhất ngay sau khi đăng nhập nếu có phiên bản mới | Must Have |
| MOB-02 | Trang chủ hiển thị banner tình trạng hồ sơ tai nạn mới nhất (nếu có) | Must Have |
| MOB-03 | Flash Cards động: Hiện card Báo cáo tai nạn nếu đã có Plan; hiện card giới thiệu gói nếu chưa có Plan | Must Have |
| MOB-04 | Điều hướng Bottom Navigation: Home, Ưu đãi, Community (Hold), Thông báo, My Page | Must Have |

---

### 2.10 [MOBILE APP] Danh sách & Chi tiết Gói Bảo hiểm (Plan List & Detail)

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| MOB-05 | Hiển thị danh sách gói với Tab: Đề xuất (추천) / Lợi ích của tôi (내 혜택) | Must Have |
| MOB-06 | Hiển thị chi tiết gói: Tên, Mô tả, Giá (tháng/năm), Quyền lợi (Icon+Title+Limit), Exclusion | Must Have |
| MOB-07 | Tất cả dữ liệu gói (Giá, Benefits, Exclusion) phải được đồng bộ từ cấu hình Admin | Must Have |
| MOB-08 | Nút "가입하기" (Đăng ký) dẫn đến trang đăng ký gói | Must Have |
| MOB-09 | Link "환불 정책" dẫn đến trang chính sách hoàn tiền | Should Have |

---

### 2.11 [MOBILE APP] Danh sách Thông báo (Notification List)

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| MOB-10 | Hiển thị danh sách thông báo, phân biệt trạng thái Read/Unread | Must Have |
| MOB-11 | Nội dung Title & Body thông báo lấy từ template Admin đã cấu hình | Must Have |
| MOB-12 | Click vào thông báo dẫn đến trang chi tiết hồ sơ tai nạn tương ứng (Deep Link) | Must Have |
| MOB-13 | Real-time update khi có thay đổi trạng thái hồ sơ tai nạn | Must Have |
| MOB-14 | Thao tác nhanh: Đánh dấu đã đọc / Xóa thông báo | Must Have |

---

### 2.12 [MOBILE APP] Trang cá nhân (My Page / Profile)

**Mục tiêu:** Cung cấp trung tâm quản lý thông tin cá nhân, tài khoản và các liên kết nhanh đến lịch sử hoạt động.

**Yêu cầu chức năng:**

| ID | Yêu cầu | Mức độ ưu tiên |
|---|---|---|
| PRF-01 | Hiển thị tóm tắt: số gói đã đăng ký, số xe đã đăng ký, số báo cáo tai nạn | Must Have |
| PRF-02 | Hiển thị thông tin tài khoản (Read Only): Tên, Account ID, Công ty, SĐT, Email | Must Have |
| PRF-03 | Chỉnh sửa thông tin cá nhân: Account ID, Tên, SĐT, Email | Must Have |
| PRF-04 | Đổi mật khẩu với Password Rules: tối thiểu 8 ký tự, 1 chữ cái, 1 chữ số | Must Have |
| PRF-05 | Rút tài khoản (Withdraw) với màn xác nhận đầy đủ cảnh báo | Must Have |
| PRF-06 | Xem danh sách điều khoản đã đồng ý (Read Only) | Must Have |
| PRF-07 | Liên kết đến danh sách xe đã đăng ký (chỉ xem, không cho sửa) | Must Have |
| PRF-08 | Liên kết đến lịch sử báo cáo tai nạn | Must Have |
| PRF-09 | Đăng xuất (Logout) | Must Have |

**Quy tắc nghiệp vụ:**
- **Vehicle List:** Người dùng **không được phép sửa thông tin xe** từ Mobile App. Mọi thay đổi biển số phải do Admin thực hiện qua Bulk Vehicle Update.
- **Withdraw Account:** Nút xác nhận xóa tài khoản chỉ active khi user đã check vào checkbox đồng ý.
- **Agreements:** Màn xem điều khoản là Read Only — không cho phép bỏ chọn các điều khoản bắt buộc.
- Khi rút tài khoản: tự động hủy tất cả hợp đồng bảo hiểm đang active và thông báo khả năng hoàn tiền.

---

## 3. Yêu cầu Phi chức năng

| Hạng mục | Yêu cầu |
|---|---|
| **Hiệu năng** | Màn hình Home cache dữ liệu local và refresh khi mở app |
| **Bảo mật** | API check T&C version phải là request đầu tiên sau khi login |
| **Toàn vẹn dữ liệu** | Bulk import phải nằm trong Transaction; nếu lỗi thì rollback toàn bộ |
| **Khả năng mở rộng** | Bulk file > 500 dòng phải xử lý bất đồng bộ (Async) và notify khi xong |
| **Idempotency** | Tránh duplicate dữ liệu khi Admin import cùng 1 file nhiều lần |
| **Audit Trail** | Ghi log đầy đủ cho: Thay đổi giá gói, Thay đổi biển số xe, Import tai nạn |

---

## 4. Luồng Dữ liệu Chính (Key Data Flows)

### 4.1 Luồng Thông báo (Notification Flow)
```
Admin cấu hình Template → Hệ thống lưu template
→ Trigger event (e.g., accident status change)
→ Worker gửi notification
→ Mobile App nhận & hiển thị (dùng Title/Body từ template)
→ User click → Deep link đến Accident Report Detail
```

### 4.2 Luồng Gán Chứng chỉ (Certificate Assignment Flow)
```
Admin chọn Applicants (Not assigned only)
→ Tìm kiếm Certificate
→ Hệ thống validate Plan matching
→ Hiển thị Valid/Invalid
→ Admin xác nhận gán Valid records
→ Cập nhật Certificate No. vào Plan Subscription
```

### 4.3 Luồng Hiển thị Gói (Plan Display Flow)
```
Admin cấu hình Plan (Tên, Giá, Benefits, Exclusion)
→ API trả dữ liệu cho Mobile App
→ Mobile Plan List hiển thị thẻ gói
→ User click → Plan Detail (Giá, Benefits, Exclusion từ Admin config)
→ User click Đăng ký → Trang Plan Registration
```

---

## 5. Bảng Module & Mức độ Ưu tiên

| Module | Platform | Mức độ ưu tiên | Trạng thái |
|---|---|---|---|
| Notification Management | Admin | P1 - Must Have | Documented |
| Affiliated Company Management | Admin | P1 - Must Have | Documented |
| Accident Management | Admin | P1 - Must Have | Documented |
| Plan Management | Admin | P1 - Must Have | Documented |
| Member Management | Admin | P1 - Must Have | Documented |
| Certificate Management | Admin | P1 - Must Have | Documented |
| Plan Subscription - Assign Certificate | Admin | P1 - Must Have | Documented |
| Plan Subscription - Bulk Update | Admin | P1 - Must Have | Documented |
| Terms & Conditions Management | Admin | P1 - Must Have | Documented |
| Home & T&C Agreement | Mobile | P1 - Must Have | Documented |
| Plan List & Detail | Mobile | P1 - Must Have | Documented |
| Notification List | Mobile | P1 - Must Have | Documented |
| My Page / Profile | Mobile | P1 - Must Have | Documented |
| GPS / Mobile Sensing | Mobile | ON HOLD | Pending |
| Community | Mobile | ON HOLD | Pending |
| Payment Settlement | Admin | ON HOLD | Pending |

---

## 6. Glossary (Thuật ngữ)

| Thuật ngữ | Định nghĩa |
|---|---|
| Certificate | Chứng chỉ bảo hiểm được cấp bởi công ty bảo hiểm |
| Plan Subscription | Bản ghi đăng ký gói bảo hiểm của một người dùng cụ thể |
| Affiliated Company | Doanh nghiệp đối tác liên kết (ví dụ: Coupang, công ty logistics) |
| Soft Delete | Xóa logic bằng cách điền timestamp vào trường `delete_at` thay vì xóa vật lý khỏi DB |
| Event-driven Notification | Thông báo tự động khi hệ thống phát hiện sự kiện (e.g., status thay đổi) |
| Broadcast Notification | Thông báo gửi hàng loạt thủ công do Admin khởi tạo |
| T&C | Terms and Conditions – Điều khoản và điều kiện sử dụng |
| Deep Link | Liên kết điều hướng đến màn hình cụ thể bên trong ứng dụng |

