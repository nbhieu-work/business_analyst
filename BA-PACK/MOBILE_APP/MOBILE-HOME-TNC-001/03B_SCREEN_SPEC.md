# Screen Spec — MOBILE-HOME-TNC-001 — v1.0
**Cập nhật lần cuối:** 2026-04-01
**Liên quan:** US-Home Page, BR-Terms Update

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-TNC | Terms and Conditions Agreement | Splash/Full Modal | User (Driver) | Sau khi Login (Nếu có T&C update) | -> SCR-HOME |
| SCR-HOME | Home - Welcome | Dashboard | User (Driver) | Sau khi Login hoặc Đồng ý T&C | -> Accident Report, -> Plan Details, -> Community (Hold) |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-TNC — Terms and Conditions Agreement
**Purpose (Mục đích):** Bắt buộc người dùng đồng ý với các điều khoản và điều kiện mới nhất trước khi sử dụng ứng dụng.  
**Primary Actor:** Người dùng (Driver)  
**Entry Conditions:** Khi người dùng đăng nhập, hệ thống kiểm tra logic so sánh phiên bản T&C người dùng đã đồng ý với phiên bản hiện tại trên server. Trình chiếu màn hình này nếu có sự thay đổi.

#### A. Bố cục / Các thành phần (Chức năng)
- **Header:** Tiêu đề "정책 및 약관 업데이트" (Cập nhật chính sách và điều khoản).
- **Body Section:**
  - **Check ALL:** Tuỳ chọn "전체동의" (Đồng ý tất cả). Khi chọn sẽ tự động check tất cả các mục bên dưới.
  - **List of Clauses:** Danh sách các điều khoản kèm checkbox và icon `>` để xem chi tiết.
- **Footer Actions:** 
  - Nút `Sign out`: Để người dùng thoát tài khoản nếu không đồng ý.
  - Nút `계속하기` (Tiếp tục): 
    - **Trạng thái:** Disable nếu chưa check hết các mục [Required]. 
    - **Trạng thái:** Enable khi đã thoả mãn điều kiện.

#### B. Logic Nghiệp vụ
- Hệ thống chỉ cho phép vào màn hình Home (SCR-HOME) khi user nhấn "Tiếp tục" và data đồng ý đã được lưu vào profile.

---

### SCR-HOME — Home - Welcome
**Purpose (Mục đích):** Màn hình chính cung cấp lối tắt nhanh đến báo cáo tai nạn và thông tin bảo hiểm.  
**Primary Actor:** Người dùng (Driver)

#### A. Layout / Sections (functional)
- **Top Bar:** Hiển thị Avatar (KH) và câu chào: "환영합니다, [Tên User] 님!".
- **Section: Accident Report Banner (Vùng báo cáo tai nạn):**
  - **Trường hợp CÓ báo cáo mới nhất:** Hiện mã hồ sơ (ví dụ: `ACC-2025-001234`), ngày cập nhật cuối và trạng thái hồ sơ (ví dụ: `Submitted`).
  - **Trường hợp KHÔNG có báo cáo:** Hiển thị text giới thiệu/chào mừng: "안심하세요, 택배아빠가 도와드리겠습니다. 배송 중 발생하는..." (Yên tâm nhé, Delivery Papa sẽ giúp bạn...).
- **Section: Flash Cards (Quick Actions):**
  - **Logic Hiển thị:**
    - **User ĐÃ đăng ký ít nhất 1 Plan:** Chỉ hiện 1 card lớn dẫn đến trang **Báo cáo tai nạn**.
    - **User CHƯA đăng ký Plan nào:** Hiện các cards giới thiệu Gói bảo hiểm. Khi click sẽ dẫn đến màn hình **Plan Detail**.
- **Section: Community (ON HOLD):** Danh sách các bài viết nổi bật (오늘의 베스트). Hiện tại giữ nguyên UI nhưng chưa active chức năng chi tiết.
- **Footer (Công ty):** Hiển thị thông tin pháp lý của công ty (Tên, Mã doanh nghiệp, Địa chỉ, Số điện thoại).
- **Bottom Navigation Bar:** Home (Active), Ưu đãi, Community, Thông báo, My Page.

#### B. User Interactions
- **Click Accident Banner:** Dẫn đến trang chi tiết vụ tai nạn đó.
- **Click Flash Card (Đã có plan):** Mở form báo cáo tai nạn mới.
- **Click Flash Card (Chưa có plan):** Mở trang mua/đăng ký gói bảo hiểm.
- **Bottom Nav:** Chuyển đổi giữa các phân vùng chính của App.

---

## 3) Developer Notes
- **Performance:** Thông tin Accident Report/Plan Status nên được cache local và refresh mỗi khi mở app để tránh giật lag màn hình Home.
- **T&C Check:** API check version T&C phải là API đầu tiên được gọi ngay sau khi login thành công hoặc khi app từ background quay lại (Resume).
