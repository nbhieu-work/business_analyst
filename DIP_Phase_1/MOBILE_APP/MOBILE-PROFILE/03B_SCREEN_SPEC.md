# Đặc tả màn hình — MOBILE-PROFILE-001 — v1.0
**Cập nhật lần cuối:** 2026-05-04
**Liên quan:** My Page, Profile Management, Account Settings

## 1) Screen Inventory (Danh sách màn hình)
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-PRF-01 | My Page (Profile Overview) | Screen / Tab | User (Driver) | Bottom Nav > MY | -> SCR-PRF-02, SCR-PRF-03, SCR-PRF-04, SCR-PRF-05, SCR-PRF-06, Accident History, Vehicle List |
| SCR-PRF-02 | Edit Profile | Screen (Full Page) | User (Driver) | SCR-PRF-01 (Settings Menu > 프로필 수정) | -> SCR-PRF-01 |
| SCR-PRF-03 | Change Password | Screen (Full Page) | User (Driver) | SCR-PRF-01 (Settings Menu > 비밀번호 변경) | -> SCR-PRF-01 |
| SCR-PRF-04 | Withdraw Account | Screen (Full Page) | User (Driver) | SCR-PRF-01 (Settings Menu > 회원 탈퇴) | -> App Login Screen |
| SCR-PRF-05 | Agreements Summary | Screen (Full Page) | User (Driver) | SCR-PRF-01 (가입 약관 및 정책 > >) | -> SCR-PRF-01 |
| SCR-PRF-06 | Settings Bottom Sheet | Bottom Sheet | User (Driver) | SCR-PRF-01 (Icon ⚙️ góc trên phải) | -> SCR-PRF-02, SCR-PRF-03, SCR-PRF-04, SCR-PRF-05 |

---

## 2) Screen Details (Chi tiết màn hình)

### SCR-PRF-01 — My Page (Profile Overview)
**Mục đích:** Trang tổng hợp thông tin cá nhân, số liệu tóm tắt và các điều khoản đã đồng ý của người dùng.
**Tác nhân chính:** Người dùng (Driver)
**Điều kiện truy cập:** Click vào tab "MY" ở Bottom Navigation.

#### A. Layout / Sections
- **Top Bar:**
  - Tiêu đề "MY".
  - Icon ⚙️ (Settings) góc trên phải → mở SCR-PRF-06 (Bottom Sheet).
- **Profile Header:**
  - Avatar (ảnh đại diện, mặc định icon camera nếu chưa có ảnh).
  - Tên người dùng (vd: `Kim Moon`) kèm icon badge.
  - Account ID (vd: `@moonie111`).
  - Link `프로필 보기 >` (Xem hồ sơ đầy đủ).
- **Summary Cards (3 thẻ ngang):**
  - `가입한 플랜 N개` (Số gói đã đăng ký). Click → màn Plan List.
  - `등록된 차량 N대` (Số xe đã đăng ký). Click → màn **Vehicle List** (chỉ xem, không cho sửa).
  - `사고 N건 보고됨` (Số báo cáo tai nạn). Click → màn **Accident Report History**.
- **Latest Accident Update Banner:**
  - Hiển thị thông báo mới nhất về cập nhật hồ sơ tai nạn (vd: ID `20251102`, trạng thái `거절됨` - Bị từ chối).
  - Nếu không có: Ẩn section này.
- **계정 정보 (Account Information — Read Only):**
  - `이름` (Tên): Hiển thị tên đầy đủ.
  - `계정 ID` (Account ID): Hiển thị username.
  - `계열사` (Affiliated Company): Tên công ty liên kết.
  - `전화번호` (Phone): Số điện thoại.
  - `이메일` (Email): Địa chỉ email.
- **가입 계약서 (Agreements Section):**
  - Tiêu đề `가입 약관 및 정책` (Điều khoản và chính sách).
  - Ngày cập nhật lần cuối (vd: `2026년 2월 12일 업데이트`).
  - Arrow `>` → click mở SCR-PRF-05.
- **로그아웃 (Logout Button):**
  - Nút đăng xuất nằm dưới cùng trang.

---

### SCR-PRF-06 — Settings Bottom Sheet
**Mục đích:** Menu tùy chọn nhanh từ icon ⚙️.
**Giao diện:** Bottom Sheet trượt lên từ phía dưới màn hình.

#### A. Danh sách tùy chọn
| Tùy chọn | Hành động |
|---|---|
| 프로필 수정 (Edit Profile) | Mở SCR-PRF-02 |
| 비밀번호 변경 (Change Password) | Mở SCR-PRF-03 |
| Add Company | Cho phép user thêm/liên kết công ty mới |
| 개인정보처리방침 보기 (Privacy Policy) | Mở trang chính sách bảo mật |
| 회원 탈퇴 (Withdraw Account) | Mở SCR-PRF-04 (màu đỏ, cảnh báo) |

---

### SCR-PRF-02 — Edit Profile (프로필 수정)
**Mục đích:** Cho phép người dùng chỉnh sửa thông tin cá nhân cơ bản.
**Giao diện:** Full screen với top bar "프로필 수정" và nút X đóng.

#### A. Các trường nhập liệu
| Trường | Loại | Ghi chú |
|---|---|---|
| 계정 ID (Account ID) | Text Input | Có thể chỉnh sửa |
| 성명 (Tên đầy đủ) | Text Input | Có thể chỉnh sửa |
| 전화 번호 (Số điện thoại) | Text Input (Phone format) | Có thể chỉnh sửa |
| 이메일 (Email) | Text Input (Email format) | Có thể chỉnh sửa |

#### B. Footer Actions
- Nút `확인` (Xác nhận) màu cam — Submit thay đổi và quay lại SCR-PRF-01.

---

### SCR-PRF-03 — Change Password (비밀번호 변경)
**Mục đích:** Cho phép người dùng đổi mật khẩu với validation đầy đủ.
**Giao diện:** Full screen với top bar "비밀번호 변경" và nút X đóng.

#### A. Các trường nhập liệu
| Trường | Loại | Ghi chú |
|---|---|---|
| 현재 비밀번호 (Mật khẩu hiện tại) | Password Input (ẩn/hiện) | Bắt buộc |
| 새 비밀번호 (Mật khẩu mới) | Password Input (ẩn/hiện) | Bắt buộc, phải thỏa mãn Password Rules |
| 새 비밀번호 확인 (Xác nhận mật khẩu mới) | Password Input (ẩn/hiện) | Phải trùng với mật khẩu mới |

#### B. Password Rules (Hiển thị dạng checklist realtime)
Mật khẩu mới phải thỏa mãn **tất cả** các điều kiện sau:
- ✅ Tối thiểu **8 ký tự**.
- ✅ Ít nhất **1 chữ cái** (A-Z hoặc a-z).
- ✅ Ít nhất **1 chữ số** (0-9).

*Mỗi điều kiện hiển thị với icon màu xám khi chưa đạt và chuyển màu xanh khi đã đạt.*

#### C. Footer Actions
- Nút `확인` (Xác nhận) màu cam — Chỉ enable khi tất cả điều kiện đã thỏa mãn.

---

### SCR-PRF-04 — Withdraw Account (출금 계좌 / 회원 탈퇴)
**Mục đích:** Cho phép người dùng xóa tài khoản vĩnh viễn sau khi đã được cảnh báo rõ ràng.
**Giao diện:** Full screen với top bar "출금 계좌" và nút X.

#### A. Nội dung cảnh báo
- **⚠️ Cảnh báo đỏ:** "탈퇴 시 복구가 불가능합니다. 계정 및 모든 데이터가 영구적으로 삭제됩니다." (Không thể khôi phục sau khi rút. Tài khoản và tất cả dữ liệu sẽ bị xóa vĩnh viễn.)
- **Danh sách dữ liệu sẽ bị xóa vĩnh viễn:**
  - 개인 정보 및 계정 정보 (Thông tin cá nhân và tài khoản)
  - 모든 보험 계획 및 약관 (Tất cả hợp đồng bảo hiểm và điều khoản)
  - 사고 신고 및 청구 이력 (Lịch sử báo cáo tai nạn và yêu cầu bồi thường)
  - 결제 정보 및 거래 내역 (Thông tin thanh toán và lịch sử giao dịch)
  - 업로드된 모든 문서 및 파일 (Tất cả tài liệu và file đã tải lên)
- **🟡 Cảnh báo vàng — Hợp đồng bảo hiểm đang hoạt động:**
  - "현재 활성 보험 계약이 2건 있습니다." (Hiện có 2 hợp đồng bảo hiểm đang hoạt động.)
  - "계정 탈퇴 시 해당 계약은 자동으로 종료됩니다. 미사용 보험에 대한 환불을 받을 수 있습니다." (Khi xóa tài khoản, các hợp đồng đó sẽ tự động chấm dứt. Bạn có thể nhận hoàn tiền cho bảo hiểm chưa sử dụng.)

#### B. Xác nhận
- **Checkbox:** "본 작업은 영구적이며 되돌릴 수 없음을 이해합니다. 모든 보험 계약을 해지하고 모든 데이터를 영구적으로 삭제하는 것에 동의합니다." (Bắt buộc phải check trước khi cho phép xóa.)
- **Nút `취소` (Hủy):** Quay lại SCR-PRF-01.
- **Nút `계정 삭제` (Xóa tài khoản):** Chỉ active khi đã check checkbox. Màu xám khi chưa check.

---

### SCR-PRF-05 — Agreements Summary (정책 및 약관)
**Mục đích:** Hiển thị danh sách tất cả các điều khoản và chính sách mà người dùng đã đồng ý.
**Giao diện:** Full screen với top bar "정책 및 약관 업데이트" và nút X.

#### A. Layout
- **Header:** Tiêu đề "지금 가입하기" (Điều khoản hiện tại đã đồng ký).
- **전체동의 (Đồng ý tất cả):** Checkbox tổng — trạng thái Checked vì user đã đồng ý.
- **Danh sách điều khoản (Read Only — Chỉ xem):**
  - Mỗi mục: Checkbox (checked) + Tên điều khoản + Icon `>` để xem chi tiết.
  - Ví dụ: 서비스 이용 약관, 개인정보처리방침, 개인정보 제3자 동의, 단체 운영 규약서, v.v.
  - Các điều khoản `Optional` vẫn hiển thị trạng thái đã chọn của user lúc đăng ký.

---

### SCR-PRF-07 — Vehicle List (Danh sách xe — Read Only)
**Mục đích:** Hiển thị danh sách xe đã đăng ký của người dùng. **Không cho phép chỉnh sửa.**
**Entry Point:** SCR-PRF-01 (Click thẻ `등록된 차량 N대`)

#### A. Layout
- Danh sách các xe hiển thị thông tin: Biển số xe, Loại xe, Trạng thái.
- **Không có nút Edit/Delete.** Mọi thay đổi biển số xe phải do Admin thực hiện qua chức năng Bulk Vehicle Update.
- Có thể có nút `+` thêm xe mới nếu nghiệp vụ cho phép (cần xác nhận thêm).

---

## 3) Business Rules (Quy tắc nghiệp vụ)
- **Vehicle List:** Người dùng **không được phép sửa thông tin xe** từ phía Mobile App. Mọi thay đổi biển số (Vehicle Number Update) phải do Admin thực hiện thông qua chức năng Bulk Import.
- **Withdraw Account:** Nút `계정 삭제` phải bị disabled cho đến khi user check vào checkbox xác nhận.
- **Password Change:** Phải kiểm tra mật khẩu hiện tại trước khi cho phép lưu mật khẩu mới.
- **Agreements:** Màn SCR-PRF-05 là chỉ xem (Read Only), không cho phép user bỏ chọn điều khoản bắt buộc đã đồng ý.

---

## 4) Developer Notes
- **Avatar Upload:** Cần API endpoint để upload/update ảnh đại diện.
- **Password Validation:** Thực hiện realtime validation ở phía client để feedback ngay lập tức.
- **Withdraw:** Cần trigger workflow: Hủy hợp đồng bảo hiểm đang active → Xóa dữ liệu user (Soft Delete hoặc Hard Delete tùy quyết định kiến trúc).
- **Summary Cards:** Số liệu (Plans, Vehicles, Accidents) cần được tính toán phía server và trả về cùng Profile API để tránh multiple requests.
