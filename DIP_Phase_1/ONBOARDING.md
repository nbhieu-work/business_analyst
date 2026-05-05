# 🚀 Onboarding Guide — Delivery Papa Insurance App (DIP)
**Dành cho:** Thành viên mới gia nhập dự án  
**Cập nhật lần cuối:** 2026-05-05  
**Ngôn ngữ làm việc:** Tiếng Việt (tài liệu kỹ thuật), Tiếng Hàn (giao diện người dùng cuối)

---

## 1. Dự án này là gì?

**Delivery Papa Insurance (DIP)** là một nền tảng bảo hiểm tích hợp phục vụ thị trường Hàn Quốc, được xây dựng dành riêng cho **tài xế giao hàng** (delivery drivers). Đây là hệ thống **2 tầng**:

| Tầng | Tên | Đối tượng dùng | Mô tả ngắn |
|---|---|---|---|
| **Admin Dashboard** | Web App (Internal) | Nhân viên vận hành nội bộ | Quản lý gói BH, thành viên, tai nạn, điều khoản, thông báo |
| **Mobile App** | iOS + Android | Tài xế giao hàng (Driver) | Xem gói BH, báo cáo tai nạn, nhận thông báo, quản lý hồ sơ |

### Tại sao nền tảng này tồn tại?
Các công ty giao hàng lớn tại Hàn Quốc (ví dụ: Coupang, các công ty logistics) có hàng nghìn tài xế cần được bảo hiểm tai nạn và trách nhiệm dân sự. Thay vì mỗi công ty tự quản lý, nền tảng DIP cung cấp một trung tâm tập trung để:
1. Admin tạo Certificate và upload danh sách tài xế của từng công ty (theo lô / bulk).
2. Tài xế tự xem, theo dõi gói BH và báo cáo tai nạn qua app.
3. Admin vận hành toàn bộ từ một Dashboard duy nhất.

---

## 2. Các nhân vật trong hệ thống (Actors)

Hệ thống có **2 loại người dùng thực sự** (actor có đăng nhập). Bạn cần hiểu rõ vai trò của mỗi người để đọc tài liệu một cách chính xác.

```
                    ┌──────────────────────────────────┐
                    │          DIP Platform             │
                    │                                  │
  ┌──────────────┐  │  ┌──────────────────────┐        │
  │ ADMINISTRATOR│──┼─►│   Admin Dashboard    │        │
  │  (Nội bộ)   │  │  │  (Web App)           │        │
  └──────────────┘  │  └──────────────────────┘        │
                    │           │                      │
                    │           ▼                      │
                    │  ┌──────────────────────┐        │
                    │  │   Mobile App         │        │
                    │  │  (iOS / Android)     │        │
                    │  └──────────────────────┘        │
                    │           ▲                      │
                    └───────────┼──────────────────────┘
                                │
                    ┌───────────────────────┐
                    │    DRIVER (User)      │
                    │    (Tài xế giao hàng) │
                    └───────────────────────┘
```

| Actor | Mô tả chi tiết | Dùng sản phẩm nào |
|---|---|---|
| **Administrator** | Nhân viên nội bộ của đơn vị vận hành nền tảng. Toàn quyền quản lý hệ thống. | Admin Dashboard (Web) |
| **Driver (User)** | Tài xế giao hàng — người thụ hưởng bảo hiểm. Đăng ký qua app, báo cáo tai nạn, nhận thông báo. | Mobile App |

---

## 3. Kiến trúc tổng thể (Big Picture)

### 3.1 Hai sản phẩm chính

```
Admin Dashboard                          Mobile App
─────────────────────────────────        ─────────────────────────────────
│  Notification Management        │       │  Home & T&C Agreement          │
│  Affiliated Company Management  │       │  Plan List & Detail            │
│  Accident Management            │◄────► │  Notification List             │
│  Plan Management                │       │  My Page / Profile             │
│  Member Management              │       │  Accident Report History       │
│  Certificate Management         │       │  Vehicle List (read-only)      │
│  Plan Subscription              │       └────────────────────────────────
│  Terms & Conditions Management  │
└─────────────────────────────────
```

### 3.2 Luồng nghiệp vụ cốt lõi

Dưới đây là **3 luồng quan trọng nhất** bạn cần nắm:

#### 🔄 Luồng 1 — Onboard Tài xế mới (Happy Path)
```
1. Affiliated Company ký hợp đồng với nền tảng
   → Admin tạo Affiliated Company trong hệ thống

2. Admin tạo Certificate (Chứng chỉ bảo hiểm)
   → Upload file Excel danh sách tài xế của công ty
   → Hệ thống tự động tạo tài khoản Driver nếu chưa có
   → Gán Plan (gói BH) cho từng Driver theo xe

3. Driver tải app, đăng nhập, đồng ý T&C
   → Xem gói BH của mình
   → App hiển thị thông tin Plan đã được Admin gán sẵn
```

#### 🔄 Luồng 2 — Báo cáo và Xử lý Tai nạn
```
1. Driver gặp tai nạn → Báo cáo qua Mobile App

2. Hồ sơ xuất hiện trong Admin > Accident Management
   (Trạng thái: RECEIVED)

3. Admin (hoặc đội xử lý) cập nhật kết quả:
   - Cập nhật thủ công từng hồ sơ, hoặc
   - Import hàng loạt bằng file Excel
   (Trạng thái: UNDER REVIEW → APPROVED hoặc REJECTED)

4. Hệ thống gửi Notification tự động đến Driver (Event-driven)
   → Driver nhận thông báo trên app
   → Click → xem chi tiết hồ sơ (Deep Link)
```

#### 🔄 Luồng 3 — Thay đổi Biển số xe
```
Vấn đề: Driver đổi xe nhưng vẫn dùng tài khoản cũ
→ Admin cần update biển số xe trong hệ thống
→ Không làm thủ công từng người, dùng Bulk Import:
   1. Admin chuẩn bị Excel: Old plate | Name | DOB | Phone | New plate
   2. Hệ thống validate 6 bước (xem chi tiết Section 5)
   3. Cập nhật đồng thời 3 bảng: Vehicle + User + Plan Subscription
   4. Ghi Audit Log đầy đủ
```

---

## 5. Chi tiết từng Module (Module Deep Dive)

### 5.1 [ADMIN] Notification Management
**Vấn đề giải quyết:** Admin cần gửi thông báo đến Driver khi có sự kiện (tai nạn được cập nhật) hoặc gửi thông báo hàng loạt (Broadcast).

**Khái niệm quan trọng:**
- **Event-driven:** Thông báo tự động khi hệ thống phát hiện sự kiện (vd: tai nạn chuyển sang `APPROVED`). Admin chỉ cần cấu hình template 1 lần.
- **Broadcast:** Admin chủ động gửi thủ công đến nhóm người dùng chỉ định.
- **Template với Dynamic Variables:** Admin viết template kiểu `"Xin chào {{username}}, hồ sơ {{case_id}} đã được phê duyệt"`. Hệ thống tự điền giá trị thực khi gửi.

**Rule quan trọng:**
- Nếu là Event-driven → bắt buộc chọn Trigger/Event Key.
- Nếu là Broadcast → mới hiển thị phần Target Audience.

---

### 5.2 [ADMIN] Affiliated Company Management
**Vấn đề giải quyết:** Quản lý danh sách đối tác doanh nghiệp — những công ty mua bảo hiểm tập thể cho đội tài xế.

**Rule quan trọng:**
- **Không được xóa** công ty để bảo toàn lịch sử. Chỉ chuyển sang `Inactive`.
- Khi `Inactive`: công ty không xuất hiện trong dropdown cho người dùng mới, nhưng vẫn giữ dữ liệu cũ.
- Format mã đăng ký: `XXX-XX-XXXXX` (số đăng ký kinh doanh Hàn Quốc).

---

### 5.3 [ADMIN] Accident Management
**Vấn đề giải quyết:** Tiếp nhận và cập nhật kết quả xử lý hồ sơ tai nạn từ Driver báo cáo qua app.

**Vòng đời trạng thái hồ sơ:**
```
RECEIVED → UNDER REVIEW → APPROVED
                        → REJECTED
```

**Tính năng Bulk Import kết quả:**
- Admin nhận file Excel từ công ty bảo hiểm chứa kết quả xử lý.
- Upload vào hệ thống, hệ thống map theo cặp key: `certificate no.` + `vehicle no.`
- Rule bỏ qua (Skip): Nếu thiếu 1 trong 3 trường `certificate no.`, `vehicle no.`, `status` → bỏ qua dòng đó.
- Nếu 1 cặp xe-chứng chỉ có nhiều hồ sơ → chỉ cập nhật hồ sơ **mới nhất**.

---

### 5.4 [ADMIN] Plan Management
**Vấn đề giải quyết:** Tạo và cấu hình các gói bảo hiểm mà Driver có thể đăng ký.

**Cấu trúc giá:**
```
Price (yearly) = Insurance fee + Membership fee (có VAT 10%)
```
Khi Admin thay đổi `Insurance fee` hoặc `Membership fee`, giá năm/tháng tự động được tính lại.

**Audit Log giá:** Mỗi lần thay đổi giá đều được ghi vào bảng `plan_price_logs`. Admin có thể Rollback về giá cũ bất kỳ lúc nào.

---

### 5.5 [ADMIN] Member Management
**Vấn đề giải quyết:** Admin cần xem và quản lý toàn bộ tài khoản Driver trên nền tảng.

**Side Drawer chi tiết có 3 tab:**
| Tab | Nội dung |
|---|---|
| Personal Information | Thông tin cá nhân (có thể sửa), danh sách xe |
| Contracts | Danh sách hợp đồng bảo hiểm đang có |
| Others | Thống kê (Reports/Policies/Claims) + Agreement Logs |

**Rule quan trọng:**
- Số CCCD chỉ hiển thị **7 số đầu**, phần còn lại mask (`●●●●●●`) — bắt buộc xử lý từ server.
- Tab **Contracts** liên kết trực tiếp với dữ liệu từ **Certificate Management**.
- Agreement Logs phân nhóm theo điều khoản, 3 trạng thái: `AGREED` / `NOT AGREED` / `NO RESPONSE`.

---

### 5.6 [ADMIN] Certificate Management (Module phức tạp nhất)
**Vấn đề giải quyết:** Một "Certificate" (Chứng chỉ bảo hiểm) là tài liệu pháp lý từ công ty bảo hiểm. Admin tạo Certificate để liên kết nhiều Driver thuộc 1 công ty với 1 gói BH cụ thể.

**Bulk Mapping Flow (luồng quan trọng):**
```
Admin chọn Plan + Company
→ Upload Excel danh sách Driver (Vehicle No, Name, Phone, ...)
→ Hệ thống xử lý từng dòng:
   ✓ User đã có → dùng User ID hiện có
   ✗ User chưa có → tạo tài khoản mới tự động
   ✓ Xe chưa có gói loại này → gán Plan mới (Pending Activation)
   ✗ Xe đã có gói cùng loại → bỏ qua (skip)
→ Kích hoạt khi upload PDF Certificate
```

**Rule Duy nhất 1 Plan/xe:**
> Một phương tiện (xe) có thể tham gia nhiều gói **khác loại** (vd: vừa có Driving Care vừa có General Liability), nhưng chỉ được có **tối đa 1 gói** cho mỗi Plan Type.

**Điều kiện Active Certificate:**
1. File PDF chứng chỉ đã được upload.
2. Ngày hiện tại nằm trong khoảng `Start Date` – `End Date`.

---

### 5.7 [ADMIN] Plan Subscription
**Vấn đề giải quyết:** Sau khi Certificate được tạo, Admin cần gán (assign) Certificate vào từng đơn đăng ký của Driver.

**Assign Certificate Flow:**
```
Admin chọn tối đa 30 đơn đăng ký (chưa có Certificate)
→ Tìm kiếm Certificate
→ Hệ thống validate: Plan của Certificate có khớp Plan của đơn không?
  ✓ Khớp → VALID
  ✗ Không khớp → INVALID (lý do: "The insurance plan don't match.")
→ Admin chỉ gán các đơn VALID
→ Đơn đã có Certificate: checkbox bị disabled (không cho gán đè)
```

**Bulk Vehicle Update (Thay đổi biển số hàng loạt):**

Logic 6 bước — **tất cả 6 bước phải pass** mới thực hiện update:

| Bước | Kiểm tra |
|---|---|
| B1 | Validate format: không field nào null, DOB/Phone/Biển số đúng format |
| B2 | Old vehicle number phải tồn tại trong hệ thống |
| B3 | Verify User: Name + DOB + Phone + Affiliated Company phải khớp với xe cũ |
| B4 | New vehicle number không được tồn tại sẵn và không được trùng xe cũ |
| B5 | Update đồng thời 3 bảng: `Vehicle` + `User` + `Plan Subscription` |
| B6 | Ghi Audit Log: Old plate, New plate, Updated by, Date, Time |

**Offboard Bulk (Nghỉ việc hàng loạt):**
- Driver nghỉ việc → cần hủy đăng ký BH.
- Không xóa cứng (hard delete) — dùng **Soft Delete**: điền timestamp vào trường `delete_at`.
- Verify 5 trường: Vehicle number + Name + DOB + Phone + Affiliated Company.

---

### 5.8 [ADMIN] Terms & Conditions Management
**Vấn đề giải quyết:** Khi nền tảng cập nhật điều khoản, Driver phải xác nhận đồng ý trước khi tiếp tục dùng app.

**Vòng đời phiên bản T&C:**
```
Draft → Scheduled (lên lịch xuất bản) → Published (đang áp dụng)
```

Mỗi điều khoản có thể là:
- **Bắt buộc (Required):** Driver buộc phải đồng ý.
- **Tùy chọn (Optional):** Driver có thể bỏ qua.
- **Đọc (Read-only):** Chỉ để thông tin, không cần đồng ý.

---

### 5.9 [MOBILE APP] Các màn hình chính

| Màn hình | Mô tả | Note quan trọng |
|---|---|---|
| **Home** | Dashboard cá nhân, hiển thị banner hồ sơ tai nạn mới nhất | Flash Cards thay đổi tùy trạng thái: có Plan hay chưa |
| **T&C Agreement** | Bắt buộc khi login nếu có phiên bản T&C mới | API check T&C phải là request đầu tiên sau login |
| **Plan List/Detail** | Xem danh sách gói BH, chi tiết giá/quyền lợi | Dữ liệu 100% đồng bộ từ Admin config |
| **Notification List** | Danh sách thông báo Read/Unread, Deep Link vào hồ sơ | Real-time update khi status hồ sơ thay đổi |
| **My Page / Profile** | Xem/sửa thông tin cá nhân, đổi mật khẩu, rút tài khoản | Xem xe (read-only), xem điều khoản đã đồng ý |

---

## 6. Quy tắc Kỹ thuật quan trọng (Technical Rules)

Đây là những quy tắc kỹ thuật cốt lõi mà **cả Dev và BA đều cần nắm**:

| Quy tắc | Mô tả | Lý do |
|---|---|---|
| **Transaction cho Bulk** | Mọi Bulk Import phải nằm trong DB Transaction. Nếu có lỗi ở bất kỳ bước nào → Rollback toàn bộ | Tránh dữ liệu inconsistent |
| **Async cho file lớn** | File Bulk > 500 dòng phải xử lý bất đồng bộ (Background Job). Notify Admin khi xong | Tránh timeout, cải thiện UX |
| **Soft Delete** | Không bao giờ xóa cứng dữ liệu nghiệp vụ quan trọng. Dùng trường `delete_at` | Bảo toàn lịch sử, hỗ trợ audit |
| **Idempotency** | Import cùng 1 file nhiều lần không được tạo duplicate data | Tài xế retry nếu gặp lỗi mạng |
| **Audit Trail** | Bắt buộc ghi log cho: Thay đổi giá gói, Thay đổi biển số, Import tai nạn | Tuân thủ, truy vết sự cố |
| **CCCD Masking** | Số CCCD chỉ hiển thị 7 số đầu — mask từ server, không phải từ client | Bảo mật dữ liệu cá nhân |
| **T&C check sau login** | API check phiên bản T&C mới nhất phải là call đầu tiên sau khi user login | Đảm bảo compliance |
| **Vehicle readonly trên app** | Driver không được sửa biển số xe từ Mobile App | Chỉ Admin mới có quyền qua Bulk Update |

---

## 7. Glossary (Từ điển thuật ngữ)

| Thuật ngữ | Định nghĩa |
|---|---|
| **Certificate** | Chứng chỉ bảo hiểm (tài liệu pháp lý) do công ty BH cấp. 1 Certificate cover nhiều Driver của 1 công ty. |
| **Plan** | Gói bảo hiểm (vd: "Chubb Safe Delivery"). Có cấu hình giá, quyền lợi, điều khoản loại trừ. |
| **Plan Type** | Loại gói (vd: Driving Care, General Liability). 1 xe chỉ được có 1 gói per Plan Type. |
| **Plan Subscription** | Bản ghi đăng ký gói BH của 1 Driver cụ thể (liên kết User + Vehicle + Plan). |
| **Affiliated Company** | Doanh nghiệp đối tác (vd: Coupang). Họ mua BH tập thể cho đội tài xế. |
| **Soft Delete** | Xóa logic: điền timestamp vào trường `delete_at` thay vì xóa khỏi DB. |
| **Event-driven Notification** | Thông báo tự động khi hệ thống phát hiện sự kiện (vd: tai nạn APPROVED). |
| **Broadcast Notification** | Thông báo gửi hàng loạt thủ công do Admin khởi tạo. |
| **T&C** | Terms and Conditions — Điều khoản và điều kiện sử dụng. |
| **Deep Link** | Liên kết điều hướng đến màn hình cụ thể bên trong app (không chỉ mở app). |
| **Bulk Import** | Nhập dữ liệu hàng loạt qua file Excel thay vì nhập thủ công từng dòng. |
| **Audit Log** | Nhật ký ghi lại ai đã thay đổi gì, lúc nào — không thể sửa/xóa. |
