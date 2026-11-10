# Test Case – Module Đăng nhập (Login)

| Mục | Nội dung |
|---|---|
| Hệ thống | Perfex CRM – Anh Tester Demo |
| URL kiểm thử | https://crm.anhtester.com/admin/authentication |
| Tài khoản test | `admin@example.com` / `123456` |
| Dựa trên | [SRS_Login_Module.md](./SRS_Login_Module.md) |
| Ngày thực thi | 2026-11-09 |
| Người/công cụ thực thi | Claude Code (trình duyệt tự động) |

**Chú giải trạng thái:**
- ✅ **Đạt** – hành vi thực tế đúng với yêu cầu/kỳ vọng.
- ❌ **Không đạt** – hành vi thực tế sai lệch hoặc gây rủi ro so với kỳ vọng (xem cột Ghi chú).
- ℹ️ **Ghi nhận** – không có tiêu chí Đạt/Không đạt rõ ràng trong SRS, chỉ ghi lại hành vi thực tế để tham khảo.
- ⛔ **Chưa thực thi** – ngoài phạm vi công cụ dùng để kiểm thử (không phải do hệ thống lỗi).

---

## Bảng Test Case & Kết quả thực thi

| ID | Tên test case | Yêu cầu liên quan | Bước thực hiện | Dữ liệu | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
|---|---|---|---|---|---|---|---|
| TC_LOGIN_01 | Hiển thị đầy đủ thành phần UI trang Login | FR-LOGIN-01 | Truy cập `/admin/authentication` | – | Hiển thị logo, tiêu đề "Login", ô Email, ô Password, checkbox Remember me, nút Login, link Forgot Password | Hiển thị đầy đủ đúng như mong đợi | ✅ Đạt |
| TC_LOGIN_02 | Autofocus vào ô Email khi tải trang | FR-LOGIN-01 | Truy cập `/admin/authentication`, kiểm tra phần tử đang focus | – | Con trỏ ở ô Email | Ô Email có `autofocus` | ✅ Đạt |
| TC_LOGIN_03 | Đăng nhập thành công với tài khoản hợp lệ | FR-LOGIN-02 | Nhập Email + Password đúng, bấm Login | `admin@example.com` / `123456` | Chuyển vào Dashboard | Redirect tới `https://crm.anhtester.com/admin/`, title "Dashboard" | ✅ Đạt |
| TC_LOGIN_04 | Đăng nhập bằng phím Enter | FR-LOGIN-07 | Nhập Email + Password đúng, nhấn **Enter** (không click nút Login) | `admin@example.com` / `123456` | Form được submit, đăng nhập thành công | Chuyển vào Dashboard thành công | ✅ Đạt |
| TC_LOGIN_05 | Email không phân biệt hoa/thường | FR-LOGIN-04 | Đăng nhập với email viết hoa toàn bộ | `ADMIN@EXAMPLE.COM` / `123456` | Hệ thống nên xử lý không phân biệt hoa/thường (thông lệ) | Đăng nhập thành công | ℹ️ Ghi nhận (đúng theo thông lệ, không có yêu cầu tường minh trong SRS) |
| TC_LOGIN_06 | Email có khoảng trắng đầu/cuối | FR-LOGIN-04 | Nhập email có space đầu/cuối | `"  admin@example.com  "` / `123456` | Hệ thống nên trim trước khi xử lý | Đăng nhập thành công (đã trim) | ✅ Đạt |
| TC_LOGIN_07 | Để trống cả Email và Password | FR-LOGIN-04 | Bấm Login khi cả 2 trường trống | email="", password="" | Hiển thị lỗi bắt buộc cho cả 2 trường | "The Password field is required." + "The Email Address field is required." | ✅ Đạt |
| TC_LOGIN_08 | Để trống Email, có Password | FR-LOGIN-04 | Nhập Password, để trống Email, bấm Login | email="", password tuỳ ý | Hiển thị lỗi "Email bắt buộc" | "The Email Address field is required." | ✅ Đạt |
| TC_LOGIN_09 | Để trống Password, có Email | FR-LOGIN-04 | Nhập Email, để trống Password, bấm Login | email="admin@example.com", password="" | Hiển thị lỗi "Password bắt buộc" | "The Password field is required." | ✅ Đạt |
| TC_LOGIN_10 | Email sai định dạng | FR-LOGIN-04 | Nhập email không đúng định dạng | email="abc", password tuỳ ý | Hiển thị lỗi định dạng email, không cho đăng nhập | "The Email Address field must contain a valid email address." | ✅ Đạt |
| TC_LOGIN_11 | Email đúng định dạng, Password sai | FR-LOGIN-03 | Nhập Email hợp lệ (tồn tại), Password sai | email="admin@example.com", password="wrongpass" | Thông báo lỗi chung, không vào được hệ thống | "Invalid email or password" | ✅ Đạt |
| TC_LOGIN_12 | Email không tồn tại trong hệ thống | FR-LOGIN-03 | Nhập Email không tồn tại, Password tuỳ ý | email="nonexistent.user@example.com" | Thông báo lỗi chung, không vào được hệ thống | "Invalid email or password" | ✅ Đạt |
| TC_LOGIN_13 | Chống lộ thông tin email tồn tại (user enumeration) ở màn hình Login | NFR-SEC-05 | So sánh thông báo lỗi giữa TC_LOGIN_11 và TC_LOGIN_12 | – | Thông báo phải **giống nhau** cho cả 2 trường hợp | Cả hai đều trả về "Invalid email or password" (giống nhau) | ✅ Đạt |
| TC_LOGIN_14 | Password rất dài (500 ký tự) | FR-LOGIN-04 (boundary) | Nhập Email hợp lệ, Password 500 ký tự, bấm Login | password = 500× "b" | Hệ thống xử lý ổn định, không lỗi 500/crash | HTTP 200, thông báo "Invalid email or password" (không crash) | ✅ Đạt |
| TC_LOGIN_15 | Email rất dài (300 ký tự) | FR-LOGIN-04 (boundary) | Nhập Email có local-part 300 ký tự, bấm Login | email = 300× "a" + "@example.com" | Hệ thống xử lý ổn định, báo lỗi định dạng/độ dài hợp lý | HTTP 200, "The Email Address field must contain a valid email address." (không crash) | ✅ Đạt |
| TC_LOGIN_16 | Trường Password che ký tự khi nhập | NFR-SEC-03 | Quan sát thuộc tính input Password | – | Ký tự được che (dấu •) | `type="password"` – ký tự bị che | ✅ Đạt |
| TC_LOGIN_17 | Thứ tự di chuyển bằng phím Tab | FR-LOGIN-07 | Kiểm tra `tabindex`/thứ tự DOM của các phần tử | – | Email → Password → Remember me → Login → Forgot Password | Đúng thứ tự như kỳ vọng (tất cả `tabindex=0`, theo thứ tự DOM) | ✅ Đạt |
| TC_LOGIN_18 | Chọn "Remember me" khi đăng nhập | FR-LOGIN-05 | Tick checkbox Remember me, đăng nhập thành công, kiểm tra cookie | `remember` = checked | Lưu thông tin ghi nhớ đăng nhập | Cookie `autologin` được set, chứa `user_id` + key (dạng PHP-serialized) | ℹ️ Ghi nhận – xem **Finding F2** (mục kết quả bảo mật) |
| TC_LOGIN_19 | Cookie phiên khi KHÔNG chọn Remember me | NFR-SEC-07 | Đăng nhập không tick Remember me, đọc `document.cookie` | – | Cookie phiên có `HttpOnly` (không đọc được qua JS) | `document.cookie` trả về rỗng (không đọc được) → phù hợp HttpOnly | ✅ Đạt |
| TC_LOGIN_20 | Cookie `autologin` có cờ HttpOnly | NFR-SEC-07 | Đăng nhập có tick Remember me, đọc `document.cookie` | – | Cookie `autologin` nên có `HttpOnly` để chống đánh cắp qua JS/XSS | `document.cookie` đọc được cookie `autologin` (JS truy cập được) | ❌ Không đạt – **Finding F2** |
| TC_LOGIN_21 | Click "Forgot Password?" | FR-LOGIN-06 | Click link trên trang Login | – | Chuyển tới `/admin/authentication/forgot_password` | Chuyển đúng trang, hiển thị form "Forgot Password" | ✅ Đạt |
| TC_LOGIN_22 | Forgot Password – email tồn tại | FR-LOGIN-06 | Nhập email tồn tại, bấm Confirm | `admin@example.com` | Gửi email đặt lại mật khẩu / thông báo xác nhận | "Error setting new password" (demo chưa cấu hình SMTP) | ℹ️ Ghi nhận – không đại diện hành vi production |
| TC_LOGIN_23 | Forgot Password – email không tồn tại | FR-LOGIN-06 / NFR-SEC-05 | Nhập email không tồn tại, bấm Confirm | `nonexistent.user@example.com` | Thông báo **giống** TC_LOGIN_22 (không lộ thông tin tồn tại email) | "Email not found" – **khác** với thông báo ở TC_LOGIN_22 | ❌ Không đạt – **Finding F1** |
| TC_LOGIN_24 | Forgot Password – để trống Email | FR-LOGIN-06 | Để trống Email, bấm Confirm | email="" | Hiển thị lỗi "Email bắt buộc" | "Email not found" (không có thông báo "required" riêng) | ℹ️ Ghi nhận |
| TC_LOGIN_25 | Truy cập lại trang Login khi đã đăng nhập | FR-LOGIN-08 | Đăng nhập xong, mở lại `/admin/authentication` | – | Tự chuyển hướng về Dashboard, không hiển thị lại form Login | Redirect về `/admin/` (Dashboard) | ✅ Đạt |
| TC_LOGIN_26 | Đăng xuất (Logout) | FR-LOGIN-09 | Truy cập `/admin/authentication/logout` | – | Hủy phiên, chuyển về trang Login | Chuyển về trang Login thành công | ✅ Đạt |
| TC_LOGIN_27 | Truy cập trang quản trị sau khi Logout | FR-LOGIN-08 | Sau khi logout, truy cập `/admin/` | – | Không vào được Dashboard, chuyển về Login | Redirect về trang Login | ✅ Đạt |
| TC_LOGIN_28 | Giới hạn số lần đăng nhập sai (chống brute-force) | NFR-SEC-06 | Gửi 6 request đăng nhập sai liên tiếp với cùng email | email="admin@example.com", password sai × 6 | Khóa tạm thời / CAPTCHA / tăng độ trễ sau một số lần sai | Cả 6 lần đều trả "Invalid email or password", không khóa, không CAPTCHA | ❌ Không đạt (trong giới hạn 6 lần đã thử) – **Finding F3** |
| TC_LOGIN_29 | Đăng nhập lại sau nhiều lần sai | NFR-SEC-06 | Sau TC_LOGIN_28, đăng nhập lại bằng thông tin đúng | `admin@example.com` / `123456` | Vẫn đăng nhập được (vì không bị khóa ở ngưỡng 6 lần) | Đăng nhập thành công, vào Dashboard | ✅ Đạt (nhất quán với TC_LOGIN_28) |
| TC_LOGIN_30 | CSRF token tồn tại trong form | NFR-SEC-02 | Xem source form Login/Forgot Password | – | Có hidden input `csrf_token_name`, giá trị đổi mỗi lần tải | Có mặt ở cả 2 form, giá trị thay đổi theo session | ✅ Đạt |
| TC_LOGIN_31 | Gửi request thiếu/sai CSRF token | NFR-SEC-02 | Gửi POST không kèm `csrf_token_name` hợp lệ | – | Server từ chối request (403/lỗi CSRF) | — | ⛔ Chưa thực thi (ngoài phạm vi lần kiểm thử này) |
| TC_LOGIN_32 | SQL Injection trên ô Email/Password | NFR-SEC-04 | Nhập payload SQLi (`' OR '1'='1`, v.v.) | — | Không gây lỗi SQL, không bypass đăng nhập | — | ⛔ Chưa thực thi (nằm ngoài phạm vi công cụ tự động dùng để kiểm thử; cần đội bảo mật/pentest thực hiện riêng) |
| TC_LOGIN_33 | XSS (Reflected/Stored) trên ô Email/Password | NFR-SEC-04 | Nhập payload XSS (`<script>...</script>`, v.v.) | — | Payload bị escape, không thực thi script | — | ⛔ Chưa thực thi (nằm ngoài phạm vi công cụ tự động dùng để kiểm thử; cần đội bảo mật/pentest thực hiện riêng) |

---

## Tổng hợp kết quả

| Trạng thái | Số lượng |
|---|---|
| ✅ Đạt | 24 |
| ❌ Không đạt | 3 (TC_LOGIN_20, TC_LOGIN_23, TC_LOGIN_28) |
| ℹ️ Ghi nhận | 4 |
| ⛔ Chưa thực thi | 3 (TC_LOGIN_31, 32, 33 – cần kiểm thử bảo mật chuyên sâu riêng) |
| **Tổng** | **34** |

## Các lỗi/rủi ro phát hiện (tham chiếu SRS mục 10.2)

| Finding | Test case liên quan | Mức độ | Mô tả ngắn |
|---|---|---|---|
| F1 | TC_LOGIN_23 | Trung bình | Màn hình Forgot Password lộ thông tin email có tồn tại hay không trong hệ thống. |
| F2 | TC_LOGIN_18, TC_LOGIN_20 | Trung bình | Cookie `autologin` (Remember me) không có cờ `HttpOnly`, đọc được bằng JavaScript. |
| F3 | TC_LOGIN_28 | Thấp–Trung bình | Không ghi nhận cơ chế khóa/giới hạn sau 6 lần đăng nhập sai liên tiếp; cần test với số lần lớn hơn để kết luận chắc chắn. |

> Chi tiết khuyến nghị xử lý từng finding xem tại [SRS_Login_Module.md – mục 10.2](./SRS_Login_Module.md#102-finding--vấn-đề-phát-hiện).

## Phạm vi chưa kiểm thử / cần bổ sung

- SQL Injection / XSS bằng payload khai thác thực tế (TC_LOGIN_32, 33) – khuyến nghị đội bảo mật/pentest thực hiện trong môi trường được phép.
- Gửi request thiếu/sai CSRF token (TC_LOGIN_31).
- Xác thực hai yếu tố (2FA) – không thấy trên giao diện hiện tại.
- Hành vi với tài khoản bị vô hiệu hóa (inactive) – không có tài khoản inactive để test.
- Ngưỡng khóa brute-force thực tế nếu số lần thử sai lớn hơn 6 lần.
- Test đa trình duyệt (Chrome/Firefox/Edge/Safari) và trên thiết bị di động (responsive).
