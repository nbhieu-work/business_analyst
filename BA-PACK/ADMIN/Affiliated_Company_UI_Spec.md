# Admin - Quản lý Công ty Liên kết (Affiliated Company Management) - Đặc tả UI & Luồng công việc
**Cập nhật lần cuối:** 2026-04-01

Quản lý danh mục các đối tác doanh nghiệp và công ty liên kết. Tài liệu này định nghĩa các thành phần giao diện (UI) và quy trình nghiệp vụ để Quản trị viên (Admin) xem, tạo mới, chỉnh sửa và chuyển đổi trạng thái hoạt động của các bản ghi công ty liên kết.

## Tổng quan tài liệu

Module này cho phép Quản trị viên:
- Đăng ký công ty mới với mã số đăng ký kinh doanh chính thức.
- Cập nhật chi tiết thông tin công ty để đảm bảo tính chính xác của hồ sơ.
- Quản lý tính khả dụng của công ty (Hoạt động - Active vs. Ngừng hoạt động - Inactive) để kiểm soát các tùy chọn lựa chọn của người dùng mà không cần xóa dữ liệu lịch sử.

---

## 1. Màn hình Danh sách Công ty Liên kết (Affiliated Company List View)

Bảng điều khiển trung tâm để quản lý các mối quan hệ đối tác doanh nghiệp.

### Các tính năng
- **Điều hướng Tab (Tab Navigation)**: Lọc nhanh các công ty theo trạng thái: `ALL` (Tất cả), `ACTIVE` (Hoạt động), và `INACTIVE` (Ngừng hoạt động).
- **Thanh công cụ (Top Bar Actions)**: 
  - Nút `+ Add Company` để đăng ký một tổ chức doanh nghiệp mới.
  - Thanh tìm kiếm (`Search bar`) để tìm công ty theo tên.
- **Bảng dữ liệu (Data Table)**:
  - **No**: Số thứ tự tăng tự động.
  - **Company Name**: Tên pháp lý hoặc tên đã đăng ký của công ty liên kết (hiển thị dưới dạng link màu xanh; có thể click).
  - **Status**: Chỉ báo trực quan về trạng thái hiện tại của công ty (`Active` hoặc `Inactive`). Click vào badge sẽ mở menu thay đổi nhanh.
  - **Business registration no**: Mã số đăng ký kinh doanh chính thức (định dạng: `XXX-XX-XXXXX`).
  - **Create at**: Ngày tạo bản ghi (YYYY-MM-DD).
  - **Update at**: Ngày chỉnh sửa cuối cùng (YYYY-MM-DD).
  - **Row Action**: Nút `Edit` nằm ở phía ngoài cùng bên phải của mỗi dòng.
- **Menu Ngữ cảnh Trạng thái (Status Context Menu)**: Click vào badge Trạng thái sẽ kích hoạt một menu popover nhỏ để chuyển đổi nhanh giữa `Active` và `Inactive`. Bao gồm các nút `Cancel` (Hủy) và `Apply` (Áp dụng) để xác nhận thay đổi.
- **Phân trang (Pagination)**: Các trình điều khiển bảng tiêu chuẩn (Rows per page: 30, First, Previous, Page Indicator, Next, Last).

---

## 2. Modal Tạo mới Công ty (Create Company Modal)

Giao diện được sử dụng để đăng ký một công ty liên kết mới vào hệ thống.

### Các trường nhập liệu (Form Fields)
| Tên trường | Loại | Bắt buộc | Mô tả |
|------------|------|----------|-------------|
| **Company name** | Text Input | Có | Tên chính thức của công ty (Placeholder: "Enter company name"). |
| **Registration number** | Text Input | Có | Mã số đăng ký kinh doanh (Placeholder mask: "000 - 00 - 00000"). |

### Các thành phần (Elements)
- **Tiêu đề (Header)**: "Create company" kèm nút đóng (X).
- **Hành động (Actions)**: `Cancel` (đóng modal không lưu) và `Create Company` (gửi dữ liệu về backend). Cả hai nút nằm ở góc dưới bên phải.

---

## 3. Modal Chỉnh sửa Công ty (Edit Company Modal)

Giao diện được sử dụng để sửa đổi bản ghi doanh nghiệp hiện có.

### Các trường nhập liệu (Form Fields)
| Tên trường | Loại | Bắt buộc | Mô tả |
|------------|------|----------|-------------|
| **Company name** | Text Input | Có | Tên chính thức của công ty, đã điền sẵn dữ liệu hiện có. |
| **Registration number** | Text Input | Có | Mã số đăng ký kinh doanh, đã điền sẵn dữ liệu hiện có. |

### Các thành phần (Elements)
- **Tiêu đề (Header)**: "Edit company" kèm nút đóng (X).
- **Hành động (Actions)**: `Cancel` (đóng modal không lưu) và `Save` (áp dụng các thay đổi vào cơ sở dữ liệu).

---

## Quy trình: Vòng đời Thực thể Doanh nghiệp (Corporate Entity Lifecycle)

Quy trình quản lý xoay quanh việc lưu giữ hồ sơ chính xác và quản lý trạng thái thay vì xóa vĩnh viễn.

### Trạng thái 1: Đăng ký (Registration)
- **Kích hoạt (Trigger)**: Admin click `+ Add Company`.
- **Hành vi (Behavior)**: Modal "Create company" mở ra. Admin nhập tên công ty và mã số đăng ký. Khi click `Create Company`, một bản ghi mới được tạo. Hệ thống ghi lại thời gian hiện tại cho cả `Create at` và `Update at`. Trạng thái mặc định thường là `Active`.

### Trạng thái 2: Sửa đổi (Modification)
- **Kích hoạt (Trigger)**: Admin click nút `Edit` trên một dòng cụ thể.
- **Hành vi (Behavior)**: Modal "Edit company" mở ra với các giá trị hiện tại. Admin cập nhật tên hoặc mã số đăng ký. Thay đổi được lưu khi click `Save`, và thời gian `Update at` được làm mới theo ngày hiện tại.

### Trạng thái 3: Chuyển đổi trạng thái (Active / Inactive)
Để bảo toàn các liên kết dữ liệu lịch sử (đặc biệt là đối với người dùng và phương tiện của họ), các công ty thường không được xóa. Thay vào đó, chúng được chuyển sang trạng thái Ngừng hoạt động (Inactive).
- **Kích hoạt (Trigger)**: Admin click vào badge Trạng thái trên danh sách, chọn trạng thái mới từ menu xổ xuống và click `Apply`.
- **Hành vi (Behavior)**: 
  - **Active**: Công ty hoạt động bình thường và khả dụng để lựa chọn trong các module khác (ví dụ: khi tài xế đăng ký xe và chọn công ty liên kết).
  - **Inactive**: Công ty vẫn tồn tại trong cơ sở dữ liệu và vẫn hiển thị trên trang Admin cho mục đích lịch sử và báo cáo nhưng bị ẩn khỏi danh sách lựa chọn công khai cho việc gán người dùng mới. Thời gian `Update at` sẽ được cập nhật khi thay đổi trạng thái.
