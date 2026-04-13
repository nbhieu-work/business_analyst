# Đặc tả màn hình — BULK-MANAGEMENT-001 — v1.0
**Cập nhật lần cuối:** 2026-04-13
**Liên quan:** Quản lý hàng loạt (Bulk Update) cho Plan Subscription: Thay đổi biển số xe và Nghỉ việc (Offboard).

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-BM-01 | Bulk Update Entry | Popover/Menu | Administrator | SCR-AS-01 (Nút "Import Contract Data") | -> SCR-BM-02, SCR-BM-03 |
| SCR-BM-02 | Vehicle Number Update Modal | Modal | Administrator | SCR-BM-01 (Chọn "Vehicle Update") | -> SCR-BM-01 |
| SCR-BM-03 | User Offboard Modal | Modal | Administrator | SCR-BM-01 (Chọn "Offboard") | -> SCR-BM-01 |

---

## 2) Functional Specifications

### FNC-01: Thay đổi biển số xe hàng loạt (Vehicle Number Update Bulk)

#### A. Quy trình thực hiện (Workflow)
1.  Admin tải lên file Excel theo định dạng mẫu.
2.  Hệ thống thực hiện kiểm tra 6 bước (Logic Check).
3.  Hiển thị danh sách kết quả kiểm tra (Success/Fail) trước khi thực hiện update cuối cùng.

#### B. Định dạng File Excel (Excel Format)
- `Old vehicle number`
- `Name`
- `DOB` (Ngày sinh)
- `Phone`
- `New vehicle number`

#### C. Logic Kiểm tra (Validation Logic)
- **B1. Validate format:** Kiểm tra tất cả các trường không được trống (Not Null). DOB, Phone, Vehicle number đúng định dạng Regex hệ thống.
- **B2. Check tồn tại vehicle:** Kiểm tra `Old vehicle number` có tồn tại trong hệ thống hay không.
- **B3. Verify User Matching:** 
  - 3.1 Kiểm tra thông tin User (Name, DOB, Phone, Affiliated Company) có tồn tại trong hệ thống không.
  - 3.2 So khớp thông tin User trên với số xe cũ (`Old vehicle number`) để đảm bảo tính chính xác của chủ sở hữu xe.
- **B4. Check New Vehicle Number:** 
  - 4.1 Số xe mới **không được tồn tại** trong hệ thống trước đó.
  - 4.2 Số xe mới **không được trùng** với số xe cũ trong cùng 1 hàng data.
- **B5. Thực hiện Update (Multi-table transaction):**
  - **Table [Vehicle]:** Update `vehicle_number`: Old -> New.
  - **Table [User]:** Đồng bộ dữ liệu biển số xe mới cho User liên quan. (Nếu Old vehicle number tồn tại trong bảng user)
  - **Table [Plan Subscription]:** Cập nhật biển số xe mới trong các bản ghi đăng ký gói bảo hiểm.
- **B6. Audit log:** Ghi nhận lịch sử (Old plate, New plate, Updated by, Date, Time). Màn hiện audit log khi thay đổi: https://www.figma.com/design/1T9HFfJv5TV2dE9ZC1kdYN/Admin?node-id=10920-38836&m=dev 

---

### FNC-02: Nghỉ việc hàng loạt (Offboard Bulk)

#### A. Quy trình thực hiện (Workflow)
1.  Admin tải lên file Excel danh sách nhân viên nghỉ việc.
2.  Hệ thống xác thực thông tin và thực hiện Soft Delete.

#### B. Định dạng File Excel (Excel Format)
- `Vehicle number`
- `Name`
- `DOB`
- `Phone`

#### C. Logic Kiểm tra (Validation Logic)
- **B1. Validate format:** Không field nào được Null. DOB/Phone hợp lệ.
- **B2. Verify User Matching:** So khớp 5 trường thông tin: `Vehicle number`, `Name`, `DOB`, `Phone`, `Affiliated Company`.
- **B3. Thực hiện Offboard:** Nếu 5 trường thông tin trên khớp với thông tin trong database, thực hiện **Soft Delete** record plan subscription liên quan bằng cách cập nhật ghi nhận vào trường `delete_at` trong database.

---

## 3) Developer Notes
- **Transaction:** Việc thực hiện update hàng loạt phải nằm trong 1 Transaction để đảm bảo tính toàn vẹn dữ liệu.
- **Background Processing:** Nếu file > 500 lines, chuyển sang xử lý nền (Async) và gửi thông báo khi hoàn tất.
- **Logging:** Đảm bảo ghi đủ các trường của B6 trong FNC-01 vào bảng `audit_logs`.
