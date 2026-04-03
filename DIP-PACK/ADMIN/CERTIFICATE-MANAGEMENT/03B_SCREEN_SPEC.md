# Đặc tả màn hình — CERTIFICATE-ENROLL-001 — v1.0
**Cập nhật lần cuối:** 2026-03-19  
**Liên quan:** BA-Orchestrator, Luồng Bulk Mapping (Ánh xạ hàng loạt)

## 1) Danh sách màn hình (Screen Inventory)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points | Related US |
|---|---|---|---|---|---|---|
| SCR-01 | Securities Certificate Management | Danh sách (List) | Administrator | Admin Menu > Certificates | -> SCR-02, SCR-03 | - |
| SCR-02 | Add Certificate | Modal | Administrator | SCR-01 (Nút "Add Certificate") | -> SCR-01 | - |
| SCR-03 | Securities Certificate Details | Drawer/Side-panel | Administrator | SCR-01 (Click vào một dòng) | -> SCR-01 | - |

---

## 2) Chi tiết màn hình (Screen Details)

### SCR-01 — Securities Certificate Management (Danh sách)
**Mục đích:** Cung cấp cái nhìn tổng quan về tất cả các chứng chỉ bảo hiểm đã phát hành, phân biệt giữa loại thành viên Doanh nghiệp (Corporate) và Cá nhân (Individual).  
**Tác nhân chính:** Quản trị viên (Administrator)  
**Điều kiện truy cập:** Admin Menu > Certificates  

#### A. Bố cục / Các thành phần (Chức năng)
- **Tiêu đề (Header):** Tiêu đề ("Securities Certificate Management") kèm badge hiển thị tổng số lượng (ví dụ: "24") + Nút "+ Add Certificate" + Dropdown "Export to Excel".
- **Các Tab:** "ALL" (Tất cả), "ACTIVE" (Đang hoạt động), "INACTIVE" (Ngưng hoạt động).
- **Vùng nội dung:** Bảng dữ liệu.
  - Các cột: No (STT), Certificate No (Số chứng chỉ), Plan name (Tên gói), Insurance Company (Công ty bảo hiểm), Member type (Hiển thị dưới dạng Badge: `Corporate`, `Individual`).

#### B. Thành phần & Liên kết dữ liệu (Data Binding)
| Thành phần | Loại | Ghi chú |
|---|---|---|
| "+ Add Certificate" | Nút bấm | Mở màn hình SCR-02 |
| "Export to Excel" | Dropdown | Kích hoạt tải xuống dữ liệu hiện tại |
| Bảng dữ liệu | Bảng | Danh sách phân trang (ví dụ: 1-10 của 23) |

#### C. Tương tác người dùng
- **Click "+ Add Certificate":** Mở modal tạo mới.
- **Click vào một dòng trong bảng:** Mở drawer chi tiết bên phải.
- **Quy tắc kiểm tra trạng thái (Status Validation):** Một Chứng chỉ chỉ có thể được chuyển sang trạng thái `Active` nếu:
  1. File PDF Chứng chỉ hợp lệ đã được tải lên.
  2. Ngày hiện tại nằm trong khoảng Start Date / End Date (Ngày bắt đầu / Kết thúc) hợp lệ.

---

### SCR-02 — Add Certificate
**Mục đích:** Cho phép Admin đăng ký cấu trúc gói bảo hiểm bằng cách ánh xạ gói, thực thể doanh nghiệp và tải lên tệp Excel để đăng ký người dùng hàng loạt.  
**Tác nhân chính:** Quản trị viên (Administrator)  

#### A. Bố cục / Các thành phần (Chức năng)
- **Tiêu đề (Header):** Tiêu đề ("Add Certificate") + Nút Đóng (x).
- **Các trường nhập liệu (Bên trái):**
  - **Plan Selection:** Dropdown chọn gói bảo hiểm (lấy từ API danh sách gói) (Ví dụ: "Chubb Safe Delivery", "General Liability").
  - **Certificate No:** Ô nhập văn bản (Text Input).
  - **Insurance Company:** Lấy dữ liệu từ gói bảo hiểm đã chọn (tự động điền dữ liệu công ty bảo hiểm tương ứng với gói).
  - **Member Type:** Nút chọn (Radio buttons) (`Corporate`, `Individual`).
  - **Corporate Name:** Dropdown chọn tên doanh nghiệp. (QUY TẮC: Ẩn nếu Member Type == Individual).
  - **Certificate Status:** Nút chọn trạng thái (`Active`, `Inactive`).
- **Các trường nhập liệu (Bên phải):**
  - **User List Upload:** Vùng Kéo & Thả / Chọn file. (Chấp nhận định dạng `.xls`, `.xlsx`. Dung lượng tối đa 100MB). **QUY TẮC:** Chỉ cho phép 1 file, Ẩn nếu Member Type == Individual.
  - **Download template:** Link tải file mẫu. (QUY TẮC: Ẩn nếu Member Type == Individual).
  - **Certificate PDF Upload:** Vùng tải lên file (Chấp nhận định dạng `.pdf`). **QUY TẮC:** `Bắt buộc` nếu Trạng thái == `Active`. `Tuỳ chọn` nếu Trạng thái == `Inactive`.
  - **Date Pickers:** Start Date, End Date (Chọn ngày - YYYY-MM-DD).
  - **Date-Time Pickers:** Enroll Start At, Enroll End At (Chọn ngày giờ đăng ký - YYYY-MM-DD HH:mm).
- **Chân trang (Footer):** Nút "Cancel", Nút "Create Certificate".

#### C. Tương tác người dùng (Luồng ánh xạ hàng loạt - Bulk Mapping Flow)
1. Admin điền các thông tin cơ bản và chọn Tên doanh nghiệp (Corporate Name).
2. Admin tải lên file Excel danh sách người dùng (User List).
3. Giao diện hệ thống: Hiển thị tên file `Filename.xls` kèm icon xoá (x).
4. Admin nhấn **"Create Certificate"**.
5. **Logic Kiểm tra & Gán (Verification & Assignment):**
   - **Kiểm tra thực thể doanh nghiệp:** Xác nhận doanh nghiệp đã tồn tại / tạo mới nếu chưa có.
   - **Kiểm tra sự tồn tại của người dùng (từ Excel):**
     - Nếu người dùng đã có trong hệ thống -> Sử dụng User ID hiện có.
     - Nếu người dùng CHƯA có -> Tự động tạo tài khoản mới dựa trên dữ liệu Excel (Số điện thoại/Tên).
   - **Kiểm tra ánh xạ Gói (theo Phương tiện và Loại gói):**
     - **Quy tắc:** Một phương tiện có thể có nhiều gói bảo hiểm khác loại, nhưng chỉ có DUY NHẤT một gói cho mỗi Loại gói (Plan Type).
     - Nếu **Phương tiện** (được map với người dùng trong tệp Excel) đã có một gói bảo hiểm đang hoạt động cùng **Loại gói (Plan Type)** với gói đã chọn -> Bỏ qua việc gán gói cho phương tiện đó.
     - Nếu **Phương tiện** chưa có gói bảo hiểm thuộc Loại gói này -> Gán gói mới. (Trạng thái: Chờ kích hoạt - Pending Activation).
   - **Kích hoạt:** Liên kết file PDF Chứng chỉ với nhóm doanh nghiệp. Kích hoạt gói bảo hiểm cho những người dùng này.
6. **Trạng thái thành công:** Hiển thị thông báo "Certificate added!" ở phía dưới.

---

### SCR-03 — Securities Certificate Details
**Mục đích:** Xem và chỉnh sửa thông tin chứng chỉ hiện có, kiểm tra danh sách người dùng đã tải lên và quản lý lịch sử gia hạn.  
**Tác nhân chính:** Quản trị viên (Administrator)  
**Điều kiện truy cập:** Click vào một dòng trong SCR-01.  

#### A. Bố cục / Các thành phần (Chức năng)
- **Tiêu đề (Header):** Số chứng chỉ (Certificate No) + Badge trạng thái dạng Dropdown ("Active"/"Inactive") + Nút Đóng (x) và Điều hướng (< >).
  - **Lưu ý kiểm tra (Validation):** Dropdown chỉ cho phép đổi trạng thái sang `Active` **nếu** file PDF Chứng chỉ đã tồn tại (được tải lên qua tab Certificate File) và ngày hiện tại nằm trong khoảng Start/End Date. Nếu không, hiển thị thông báo lỗi.
- **Các Tab:** "General information" (Thông tin chung), "Certificate File" (File Chứng chỉ), "Renewal History" (Lịch sử gia hạn).
- **Tab: General Information (Khối hiển thị chỉ đọc phía trên):** Tên gói (Plan Name), Loại gói (Plan Type), Tổng số người dùng đăng ký (Total Covered Users).
- **Tab: General Information (Form Chỉnh sửa):** Certificate No, Insurance Company, Member Type, Corporate Name, Certificate Status.
- **Tab: General Information (Danh sách file):** Link tải file danh sách người dùng đã tải lên (`Filename.xls` -> "Click để tải").
- **Tab: General Information (Ngày tháng):** Start/End Dates, Enroll Dates, Ngày tạo (Created at) / Ngày cập nhật (Updated at).
- **Tab: Certificate File (Tab chuyên biệt):** Vùng để xem/tải lên/thay thế file PDF Chứng chỉ chính thức.
- **Tab: Renewal History:** Danh sách/Bảng theo dõi các lần đăng ký hoặc cập nhật chính sách trong quá khứ.
- **Chân trang (Footer):** "Delete", "Reset", "Update" (Nút chính).

#### C. Tương tác người dùng
- **Tải tệp Excel:** Click vào link tệp để tải về danh sách Excel gốc.
- **Update:** Lưu các thay đổi vào các trường thông tin của chứng chỉ.

---

## 3) Ghi chú cho Lập trình viên (Developer Notes)
- **Tham chiếu API:** Liên quan đến chứng chỉ: `/plan-certificates`.
- **Sử dụng dữ liệu:** Sau quá trình ánh xạ hàng loạt (bulk mapping) và kích hoạt, dữ liệu đăng ký kết quả phải được xem được tại trang **Plan Subscription Status** (API: `/user-plan`).
