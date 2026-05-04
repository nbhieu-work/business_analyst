# Đặc tả màn hình — ACCIDENT-MANAGEMENT-001 — v1.0
**Cập nhật lần cuối:** 2026-04-01
**Liên quan:** US-Accident Process, Bulk Results Update

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-01 | Accident Submission Manager | List | Administrator | Admin Menu > Accident history | -> SCR-02 |
| SCR-02 | Import Accident Result Data | Modal | Administrator | SCR-01 (Nút "Import Accident Data") | -> SCR-01 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-01 — Accident Submission Manager (Danh sách)
**Mục đích:** Quản lý lịch sử và trạng thái xử lý các yêu cầu báo cáo tai nạn từ phía người dùng.  
**Tác nhân chính:** Quản trị viên (Administrator)  

#### A. Bố cục / Các thành phần (Chức năng)
- **Header:** Tiêu đề "Accident Submission Manager" kèm tổng số lượng bản ghi.
- **Thanh công cụ:** 
  - Nút `Import Accident Data` để cập nhật kết quả hàng loạt.
  - Nút `Export to Excel` (Dropdown).
  - Thanh tìm kiếm `Search Accident Submission`.
- **Bộ lọc Tab:** `ALL`, `RECEIVED`, `UNDER REVIEW`, `APPROVED`, `REJECTED`.
- **Bảng dữ liệu (Data Table):**
  - Các cột quan trọng: Report Number, Report Date, Status, **Certificate No**, Name, Date of Birth, Phone number, **Vehicle Number**, Company, Accident Date & Time, Location, Injured/Damaged, Amount.
- **Thao tác nhanh:** Click vào badge Status để mở Pop-over thay đổi trạng thái nhanh (Received, Under Review, Approved, Rejected).

---

### SCR-02 — Import Accident Result Data (Modal & Logic)
**Mục đích:** Cho phép Admin tải lên tệp Excel để cập nhật kết quả xử lý tai nạn (trạng thái, số tiền bồi thường, quản lý bồi thường, v.v.) cho nhiều hồ sơ cùng lúc.  
**Tác nhân chính:** Quản trị viên (Administrator)

#### A. Giao diện (UI)
- **Component:** Pop-up Modal hiển thị chính giữa.
- **Tiêu đề:** "Import accident result data".
- **Vùng tải lên (Upload Area):** 
  - Hỗ trợ Kéo & Thả (Drag and drop) hoặc Chọn từ máy tính.
  - Định dạng cho phép: `.xlsx`, `.xls`. Dung lượng tối đa cho 1 file: 100MB.
  - **Giới hạn số lượng file:** Chỉ cho phép tải lên **tối đa 5 file** mỗi lượt xử lý.
- **Liên kết mẫu:** Nút `Download template` để Admin tải file cấu trúc chuẩn.
- **Chân trang (Footer):** Nút `Cancel`, Nút `Import`.

#### B. Cấu trúc tệp Excel (Excel Schema)
Cấu trúc file phải chứa các cột sau:
1. `certificate no.` (Mã chứng chỉ) - **Key field**
2. `vehicle no.` (Biển số xe) - **Key field**
3. `status` (Trạng thái)
4. `claim manager` (Người quản lý bồi thường)
5. `claim manager contact` (SĐT người quản lý bồi thường)
6. `loss assessment company` (Công ty giám định tổn thất)
7. `loss assessment manager` (Người giám định)
8. `loss assessment manager contact` (SĐT người giám định)
9. `claim amount` (Số tiền bồi thường)
10. `reason for rejection` (Lý do từ chối - nếu có)

#### C. Logic xử lý & Quy trình Ánh xạ (Backend Mapping Logic) - Cực kỳ quan trọng
1. **Quy tắc bỏ qua dữ liệu lỗi (Skip Line Rule):**
   - Nếu một dòng (line) trong file Excel bị **Trống (Empty)** bất kỳ trường nào trong bộ 3 trường sau: `certificate no.`, `vehicle no.`, hoặc `status`.
   - => Hệ thống sẽ **Bỏ qua** dòng dữ liệu đó, không thực hiện cập nhật và tự động chuyển sang xử lý dòng tiếp theo trong file.
2. **Quy tắc Ánh xạ (Mapping Rule):** 
   - Hệ thống sử dụng cặp tổ hợp `certificate no.` VÀ `vehicle no.` để xác định hồ sơ tai nạn cần cập nhật.
3. **Xử lý trùng lặp/Hồ sơ nhiều lần:**
   - Vì một cặp xe và chứng chỉ có thể báo cáo tai nạn nhiều lần (nhiều Report Number khác nhau), hệ thống sẽ:
     - **Tìm kiếm dữ liệu:** Quét trong database để lấy ra hồ sơ báo cáo tai nạn (Accident Report) **MỚI NHẤT** (line data mới nhất dựa trên Ngày tạo/Report Date) của cặp `certificate no.` + `vehicle no.` này.
     - **Cập nhật:** Chỉ cập nhật kết quả vào hồ sơ mới nhất được tìm thấy.
3. **Phản hồi sau khi Import (Feedback):**
   - **Thành công:** Hiển thị số lượng bản ghi thành công (e.g., `Success: 2`).
   - **Thất bại:** Hiển thị số lượng bản ghi lỗi (e.g., `Failed: 1`) kèm mô tả lỗi cụ thể dưới mỗi tên file (ví dụ: `Invalid license plate`, `Certificate not found`).
   - **Trạng thái:** "Upload completed!" hoặc "Upload failed!" nếu lỗi toàn bộ.

---

## 3) Developer Notes
- **API Reference:** `/api/accident-reports/bulk-update-results`.
- **Validation:** Phải kiểm tra tồn tại của Certificate No và Vehicle No trong hệ thống trước khi update.
- **Idempotency:** Tránh việc duplicate dữ liệu nếu admin ấn Import nhiều lần cùng 1 file.
