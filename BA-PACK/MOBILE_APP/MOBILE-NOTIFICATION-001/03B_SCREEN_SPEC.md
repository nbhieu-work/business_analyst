# Screen Spec — MOBILE-NOTIFICATION-001 — v1.0
**Cập nhật lần cuối:** 2026-04-01
**Liên quan:** US-Notifications, BR-Accident Report Trigger

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-NOTI-LIST | Notification List | Screen / Tab | User (Driver) | Bottom Nav > 알림 (Notifications) | -> Accident Report Detail, -> Delete/Read Action |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-NOTI-LIST — Notification List
**Purpose (Mục đích):** Cung cấp trung tâm thông báo tập trung cho người dùng. Hiện tại tập trung phản ánh các thay đổi trạng thái của Báo cáo tai nạn (Accident Report).  
**Primary Actor:** Người dùng (Driver)  
**Entry Conditions:** Click vào icon chuông hoặc tab "알림" ở thanh điều hướng dưới cùng.

#### A. Layout / Sections (Chức năng)
- **Top Bar:** Tiêu đề "알림" (Thông báo).
- **Content Area (Vùng nội dung):** 
  - **Trường hợp KHÔNG có dữ liệu (No Data):** Hiển thị hình minh họa (Empty box/clip) và text: "지금은 알림이 없습니다" (Hiện tại không có thông báo nào).
  - **Trường hợp CÓ dữ liệu (List View):** Hiển thị danh sách các thẻ thông báo theo chiều dọc.
- **Notification Item (Thành phần của một thông báo):**
  - **Icon trạng thái:** Phân biệt bằng màu sắc/biểu tượng (Xanh dương cho gửi thành công, Xanh lá cho được duyệt, Vàng cho đang xử lý, Đỏ cho bị từ chối).
  - **Header:** [Accident ID] đi kèm thời gian (ví dụ: `Today, 12:00` hoặc `YYYY-MM-DD`).
  - **Title:** Tiêu đề thông báo. (Dữ liệu được cấu hình từ **Noti Title** tại Admin Notification management).
  - **Body:** Nội dung mô tả chi tiết ngắn gọn. (Dữ liệu được cấu hình từ **Noti Body** tại Admin Notification management).
  - **Action Menu (Ba chấm dọc):** Nút mở Menu thao tác nhanh.
- **Bottom Navigation Bar:** Giữ nguyên các tab chính, tab Notifications ở trạng thái Active.

#### B. User Interactions (Tương tác & Flow)
- **Click vào một thông báo:** Hệ thống điều hướng người dùng trực tiếp đến trang **Accident Report Detail** tương ứng với hồ sơ đó.
- **Click vào nút Ba chấm dọc:** Mở Bottom Sheet thao tác:
  - **읽지 않음으로 표시 (Mark as Unread/Read):** Thay đổi trạng thái đọc của thông báo (hiệu ứng in đậm/nhạt).
  - **삭제 알림 (Delete Notification):** Xoá thông báo khỏi danh sách.
- **Real-time Update:** Hệ thống phải cập nhật ngay lập tức danh sách thông báo khi có bất kỳ thay đổi nào từ phía Backend liên quan đến tiến trình báo cáo tai nạn (Accident report status).

#### C. Quy tắc Backend & Notification
- **Trigger:** Hiện tại chỉ hỗ trợ trigger từ logic **Accident Report**. 
- **Content:** Toàn bộ nội dung Title và Body của thông báo được hiển thị trên In-app Notification sẽ lấy trực tiếp từ các cấu hình của module QUẢN LÝ THÔNG BÁO phía Admin.
- **Auto-Update:** Đảm bảo tính Real-time trên app.

---

## 4) Developer Notes
- **Navigation:** Deep link từ notification item phải chứa ID hồ sơ tai nạn cụ thể.
- **UI:** Đảm bảo các trạng thái thông báo đã đọc (Read) và chưa đọc (Unread) được phân biệt rõ ràng bằng màu nền hoặc độ đậm của chữ.
