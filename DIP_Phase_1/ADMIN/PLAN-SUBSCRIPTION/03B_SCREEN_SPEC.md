# Đặc tả màn hình — ASSIGN-CERTIFICATE-001 — v1.0
**Cập nhật lần cuối:** 2026-04-08
**Liên quan:** Gán chứng chỉ bảo hiểm (Assign Certificate) cho các đơn đăng ký gói (Plan Subscription).

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-AS-01 | Select Applicant | Main Screen | Administrator | Sidemenu > Plan Subscription | -> SCR-AS-02 |
| SCR-AS-02 | Assign Certificate Modal | Modal/Step | Administrator | SCR-AS-01 (Click "Assign Certificate") | -> SCR-AS-01 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-AS-01 — Select Applicant (Màn hình chính)
**Mục đích:** Admin lựa chọn các đơn đăng ký (applicants) chưa có số chứng chỉ để thực hiện gán chứng chỉ bảo hiểm.
**Tác nhân chính:** Administrator (Quản trị viên)

#### A. Layout / Components
- **Data Table:** Danh sách các đơn đăng ký gói bảo hiểm.
- **Checkbox:** Cột đầu tiên cho phép chọn nhiều hàng. 
  - **Logic:** Chỉ cho phép chọn những đơn hàng có trạng thái Certificate là `Not assigned`. 
  - **State:** Nếu đơn đã có `Certificate No.` (đã được gán), ô Checkbox sẽ bị **vô hiệu hóa (Disabled)** để ngăn chặn việc gán đè.
- **Top Bar (Dynamic):** 
  - Nút `Assign Certificate` kèm số lượng đã chọn (vd: `3 selected`). 
  - Chỉ hiển thị khi có ít nhất 1 hàng được chọn.
- **Limit Warning (Tooltip/Message):** Thông báo "You have selected up to 30 applicants!" khi số lượng chọn đạt hoặc vượt hạn mức cho phép thực hiện đồng thời.
- **Nút "Assign Certificate":** Khi bấm vào sẽ mở Popup **Assign Certificate (Step 1)**.

---

### SCR-AS-02 — Assign Certificate (Modal Workflow)
**Mục đích:** Thực hiện tìm kiếm và gán một chứng chỉ bảo hiểm (Certificate) cho các đơn đã chọn, bao gồm bước kiểm tra tính hợp lệ của Plan.

#### Step 1: Khởi tạo Popup
- **UI:** Hiện danh sách `APPLICATION LIST` (những hàng đã chọn ở SCR-AS-01) kèm trạng thái `Validation status` ban đầu là `--`.
- **Thành phần:** 
  - Dropdown `Select Certificate`: Bấm vào để mở danh sách tìm kiếm.
  - Tổng số lượng: `Total: [Tổng số lượng đã chọn]`, `Invalid: --`, `Valid: --`.

#### Step 2: Tìm kiếm và Chọn Certificate (Search Certificate)
- **Dropdown List:** 
  - Ô tìm kiếm tích hợp bộ lọc: `by all`, `number`, `by company`, `by plan`.
  - Kết quả hiển thị: `Certificate Number`, `Plan name`, `Company`, `Period` (Hiệu lực).
- **Hành động:** Admin chọn 1 Certificate cụ thể.

#### Step 3: Validation Logic (Kiểm tra điều kiện)
Sau khi Certificate được chọn, hệ thống tự động so khớp Plan của Certificate với Plan của các đơn đăng ký trong danh sách:
- **Trạng thái Valid (Hợp lệ):** Hiển thị nhãn xanh `Valid` nếu trùng khớp Plan.
- **Trạng thái Invalid (Không hợp lệ):** Hiển thị nhãn đỏ `Invalid` và cột Note hiện lý do lỗi: **"The insurance plan don't match."**
  - *Ví dụ:* Đơn đăng ký chọn Plan "Delivery Care - CPA Plan" nhưng Certificate được chọn thuộc Plan "Public Plan".
- **Summary:** Cập nhật lại số lượng `Invalid: 1`, `Valid: 5`.
- **Checkbox:** Hàng bị `Invalid` sẽ bị vô hiệu hóa checkbox (không thể gán).

#### Step 4: Thực hiện Gán (Assign)
- **Nút `Assign (n)`:** Chỉ sáng lên khi có ít nhất 1 bản ghi `Valid` được chọn.
- **Tiến trình:** Bấm `Assign` -> Hiển thị icon Loading -> [Thành công] -> Tắt Popup.
- **Thông báo thành công:** Toast message: "Successfully assigned certificate [Cert NO] to [n] applicant!".
- **Kết quả:** Quay lại SCR-AS-01, các đơn đã gán sẽ hiển thị số `Certificate No` thay vì "Not assigned".

---

## 3) Developer Notes
- **API Requirements:** 
  - Endpoint tìm kiếm Certificate (hỗ trợ search theo company/plan/number).
  - Logic so khớp `Plan_ID` giữa Certificate và Subscription.
- **UI Interaction:** 
  - Disable hàng invalid trong modal.
  - Xử lý trạng thái nút Assign động dựa trên checkbox.`
