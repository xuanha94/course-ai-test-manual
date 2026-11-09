# SRS – Module Đăng nhập (Login)

| Mục | Nội dung |
|---|---|
| Hệ thống | Perfex CRM – Anh Tester Demo |
| URL | https://crm.anhtester.com/admin/authentication |
| Module | Login (Admin Authentication) |
| Phiên bản tài liệu | 1.0 (Draft) |
| Ngày lập | 2026-11-09 |
| Nguồn | Khảo sát giao diện + thực thi các trường hợp đăng nhập/quên mật khẩu trên môi trường demo |

> **Quy ước nguồn thông tin**
> - **[Quan sát]**: xác nhận trực tiếp từ trang (DOM/giao diện) hoặc từ kết quả submit form thực tế.
> - **[Giả định]**: suy luận theo hành vi chuẩn của Perfex CRM/CodeIgniter, chưa submit thực tế để xác nhận.
>
> **Phạm vi đã kiểm thử thực tế trong tài liệu này:** để trống trường, email sai định dạng, sai mật khẩu, email không tồn tại, đăng nhập đúng (`admin@example.com`), Remember me, truy cập lại trang Login khi đã đăng nhập, Logout, truy cập `/admin` khi chưa đăng nhập, Forgot Password (email tồn tại/không tồn tại/trống), 6 lần đăng nhập sai liên tiếp (brute-force), email có khoảng trắng, email viết hoa/thường. Kết quả chi tiết ở mục **10**.
> **Chưa thực hiện:** kiểm thử SQL Injection/XSS bằng payload khai thác (ngoài phạm vi công cụ tự động dùng để lập tài liệu này — khuyến nghị đội bảo mật/pentest thực hiện riêng), kiểm thử 2FA, tài khoản bị vô hiệu hóa (inactive).

---

## 1. Giới thiệu

### 1.1 Mục đích
Mô tả yêu cầu chức năng và phi chức năng của module Đăng nhập khu vực quản trị (Admin) của Perfex CRM, làm cơ sở cho việc thiết kế test case, kiểm thử và nghiệm thu.

### 1.2 Phạm vi
- Màn hình **Login** (`/admin/authentication`).
- Liên kết **Forgot Password** (`/admin/authentication/forgot_password`) ở mức tích hợp từ màn hình Login.
- Tùy chọn **Remember me**.

Ngoài phạm vi: đăng nhập khu vực Customer (client portal), đăng ký, đổi mật khẩu sau đăng nhập, phân quyền chi tiết sau đăng nhập, 2FA (chưa quan sát thấy trên giao diện).

### 1.3 Đối tượng sử dụng
| Vai trò | Mô tả |
|---|---|
| Admin / Staff | Nhân viên có tài khoản trong hệ thống CRM, đăng nhập vào khu vực `/admin` |

### 1.4 Định nghĩa
| Thuật ngữ | Ý nghĩa |
|---|---|
| CSRF | Cross-Site Request Forgery – token chống giả mạo request |
| Staff | Tài khoản nhân viên trong Perfex CRM |

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh
Trang Login là điểm vào duy nhất của khu vực quản trị. Người dùng chưa xác thực truy cập bất kỳ trang `/admin/*` đều phải được chuyển về trang này **[Giả định]**.

### 2.2 Thông tin dữ liệu test
| Trường | Giá trị |
|---|---|
| Email hợp lệ | `admin@example.com` (do người yêu cầu cung cấp) |
| Mật khẩu hợp lệ | *Do người yêu cầu cung cấp – không lưu trong repo* |

### 2.3 Ràng buộc & giả định
- Hệ thống là web app, truy cập qua trình duyệt, giao thức HTTPS **[Quan sát]**.
- Form gửi bằng phương thức **POST** tới chính URL `/admin/authentication` **[Quan sát]**.
- Trang demo có thể bị reset dữ liệu định kỳ – tài khoản test có thể thay đổi **[Giả định]**.

---

## 3. Mô tả giao diện (UI)

**Tiêu đề trình duyệt:** `Perfex CRM | Anh Tester Demo - Login` **[Quan sát]**

### 3.1 Thành phần màn hình Login

| # | Thành phần | Loại | name / id | Thuộc tính quan sát | Ghi chú |
|---|---|---|---|---|---|
| 1 | Logo | Hình ảnh + link về `https://crm.anhtester.com/` | – | alt: "Perfex CRM \| Anh Tester Demo" | **[Quan sát]** |
| 2 | Tiêu đề | Heading | – | Text: "Login" | **[Quan sát]** |
| 3 | Email Address | Input `type="email"` | `email` / `email` | autofocus = có; `required` HTML = không; maxlength = không giới hạn; không placeholder | **[Quan sát]** – Có label |
| 4 | Password | Input `type="password"` | `password` / `password` | `required` HTML = không; không maxlength; không placeholder | **[Quan sát]** – Có label, mặc định che ký tự |
| 5 | Remember me | Checkbox | `remember` / `remember` | Mặc định không chọn | **[Quan sát]** |
| 6 | Nút Login | Button `type="submit"` | – | Text: "Login" | **[Quan sát]** |
| 7 | Forgot Password? | Link | – | href: `/admin/authentication/forgot_password` | **[Quan sát]** |
| 8 | CSRF token | Hidden input | `csrf_token_name` | Giá trị sinh động theo phiên | **[Quan sát]** |

### 3.2 Không có trên giao diện
- Không có reCAPTCHA/captcha **[Quan sát]**.
- Không có đăng nhập qua mạng xã hội/SSO **[Quan sát]**.
- Không có liên kết đăng ký tài khoản **[Quan sát]**.
- Không có nút hiện/ẩn mật khẩu **[Quan sát]**.

### 3.3 Màn hình Forgot Password (liên kết từ Login)
| Thành phần | Mô tả |
|---|---|
| Tiêu đề | "Forgot Password" |
| Email Address | Input `type="email"`, name `email`, id `email` |
| Nút | "Confirm" (submit, POST tới `/admin/authentication/forgot_password`) |
| CSRF | Hidden input `csrf_token_name` |

---

## 4. Yêu cầu chức năng

### FR-LOGIN-01: Hiển thị trang đăng nhập
- Khi truy cập `/admin/authentication`, hệ thống hiển thị form gồm các thành phần ở mục 3.1.
- Con trỏ tự động focus vào ô **Email Address** **[Quan sát: autofocus]**.
- Mức ưu tiên: Cao.

### FR-LOGIN-02: Đăng nhập thành công
- **Điều kiện:** Email và mật khẩu khớp một tài khoản staff đang hoạt động.
- **Kết quả:** Hệ thống tạo phiên đăng nhập và chuyển hướng tới `https://crm.anhtester.com/admin/` (trang Dashboard, title "Dashboard") **[Quan sát – đã test với `admin@example.com`/`123456`]**.
- Email không phân biệt hoa/thường: đăng nhập bằng `ADMIN@EXAMPLE.COM` vẫn thành công **[Quan sát]**.
- Email có khoảng trắng đầu/cuối (`"  admin@example.com  "`) vẫn đăng nhập thành công → hệ thống tự trim **[Quan sát]**.
- Mức ưu tiên: Cao.

### FR-LOGIN-03: Đăng nhập thất bại – thông tin sai
- **Điều kiện:** Email không tồn tại hoặc mật khẩu sai.
- **Kết quả:** Ở lại trang Login (HTTP 200, không redirect), hiển thị thông báo lỗi dùng chung: **"Invalid email or password"** **[Quan sát]**.
- Đã xác nhận thông báo **giống nhau** cho cả hai trường hợp "email đúng + password sai" và "email không tồn tại + password sai" → **không** lộ thông tin email có tồn tại ở màn hình Login (khác với màn hình Forgot Password, xem mục 10 – Finding F1) **[Quan sát]**.
- Mức ưu tiên: Cao.

### FR-LOGIN-04: Kiểm tra dữ liệu đầu vào (Validation)
| Trường hợp | Kết quả thực tế quan sát được |
|---|---|
| Cả hai trống | Hiển thị đồng thời 2 thông báo: "The Password field is required." và "The Email Address field is required." **[Quan sát]** |
| Chỉ Email, Password trống | "The Password field is required." **[Quan sát]** |
| Chỉ Password, Email trống | "The Email Address field is required." **[Quan sát]** |
| Email sai định dạng (vd: `abc`) | "The Email Address field must contain a valid email address." – đây là validate **server-side** (xác nhận được bằng cách submit trực tiếp qua form, bỏ qua kiểm tra `type="email"` của trình duyệt) **[Quan sát]** |
| Khoảng trắng đầu/cuối email | Được trim, không gây lỗi, đăng nhập vẫn thành công nếu đúng mật khẩu **[Quan sát]** |
| Email viết hoa/thường khác nhau | Không phân biệt hoa/thường **[Quan sát]** |
| SQL Injection / XSS payload | **Chưa kiểm thử** trong phạm vi này – khuyến nghị đội bảo mật thực hiện riêng |

> Ghi chú: các input HTML **không** có thuộc tính `required`, nhưng server-side validate vẫn chặn đầy đủ (đã quan sát) **[Quan sát]**.

### FR-LOGIN-05: Ghi nhớ đăng nhập (Remember me)
- Khi chọn "Remember me" và đăng nhập thành công, hệ thống set thêm cookie **`autologin`** chứa chuỗi PHP-serialized gồm `user_id` và một `key` tự sinh (ví dụ: `a:2:{s:7:"user_id";s:1:"2";s:3:"key";s:16:"...";}`) **[Quan sát]**.
- Cookie `autologin` **đọc được bằng `document.cookie`** (JavaScript), tức **không có cờ `HttpOnly`** → xem Finding F2 (mục 10) về rủi ro liên quan **[Quan sát]**.
- Khi **không** chọn Remember me, cookie phiên chính không đọc được qua `document.cookie` (JS trả về rỗng), phù hợp với cookie có cờ `HttpOnly` **[Quan sát]**.
- Thời hạn chính xác của cookie `autologin` chưa kiểm tra **[Cần xác nhận]**.

### FR-LOGIN-06: Quên mật khẩu
- Nhấn "Forgot Password?" chuyển tới `/admin/authentication/forgot_password` **[Quan sát]**.
- Nhập **email đã tồn tại** (`admin@example.com`) và bấm "Confirm" → thông báo **"Error setting new password"** **[Quan sát]** (có thể do môi trường demo không cấu hình gửi mail; không đại diện cho hành vi production).
- Nhập **email không tồn tại** → thông báo **"Email not found"** **[Quan sát]**.
- Để **trống** email và bấm "Confirm" → cũng trả về **"Email not found"** (không có thông báo "required" riêng) **[Quan sát]**.
- ⚠️ Thông báo khác nhau giữa email tồn tại/không tồn tại → lộ thông tin email có tồn tại trong hệ thống, xem Finding F1 (mục 10) **[Quan sát]**.

### FR-LOGIN-07: Phím tắt / thao tác bàn phím
- Nhấn **Enter** trong ô Email/Password sẽ submit form **[Giả định – hành vi chuẩn của form]**.
- Thứ tự Tab: Email → Password → Remember me → Login → Forgot Password? **[Giả định theo thứ tự DOM]**.

### FR-LOGIN-08: Kiểm soát truy cập
- Người dùng **chưa đăng nhập** (đã logout) truy cập `/admin/` bị tự động chuyển về trang Login **[Quan sát]**.
- Người dùng **đã đăng nhập** truy cập lại `/admin/authentication` được tự động chuyển về Dashboard (`/admin/`), không hiển thị lại form Login **[Quan sát]**.

### FR-LOGIN-09: Đăng xuất (tích hợp)
- Truy cập `/admin/authentication/logout` → phiên bị hủy, chuyển hướng về trang Login **[Quan sát]**.
- Sau khi đăng xuất, truy cập lại `/admin/` yêu cầu đăng nhập lại (không vào thẳng Dashboard) **[Quan sát]**; hành vi cụ thể khi dùng nút Back của trình duyệt (cache trang) chưa kiểm tra **[Cần xác nhận]**.

---

## 5. Yêu cầu phi chức năng

### 5.1 Bảo mật
| ID | Yêu cầu | Nguồn |
|---|---|---|
| NFR-SEC-01 | Truyền dữ liệu qua HTTPS | Quan sát |
| NFR-SEC-02 | Form có CSRF token (`csrf_token_name`), token thay đổi mỗi lần tải trang | Quan sát (có token) / chưa thử gửi thiếu/sai token nên hành vi từ chối **[Cần xác nhận]** |
| NFR-SEC-03 | Trường Password che ký tự (`type="password"`); mật khẩu không xuất hiện trên URL (submit bằng POST) | Quan sát |
| NFR-SEC-04 | Chống SQL Injection / XSS trên các trường nhập | **Chưa kiểm thử** (ngoài phạm vi công cụ dùng để lập tài liệu này) – khuyến nghị đội bảo mật/pentest kiểm tra riêng |
| NFR-SEC-05 | Thông báo lỗi không tiết lộ email có tồn tại hay không | **Màn hình Login: ĐẠT** (thông báo giống nhau). **Màn hình Forgot Password: KHÔNG ĐẠT** – xem Finding F1 (mục 10) **[Quan sát]** |
| NFR-SEC-06 | Cơ chế chống brute-force (khóa tạm thời/giới hạn số lần thử sai) | Đã thử 6 lần đăng nhập sai liên tiếp, **không** ghi nhận khóa tài khoản hay độ trễ tăng dần – xem Finding F3 (mục 10) **[Quan sát, giới hạn 6 lần]** |
| NFR-SEC-07 | Cookie phiên có cờ `HttpOnly`/`Secure` | Cookie phiên chính: không đọc được qua JS → phù hợp `HttpOnly` **[Quan sát]**. Cookie `autologin` (khi chọn Remember me): đọc được qua JS → **không** có `HttpOnly` – xem Finding F2 (mục 10) **[Quan sát]** |
| NFR-SEC-08 | Mật khẩu được lưu dạng băm | Không thể quan sát từ phía client (black-box) – Giả định theo chuẩn CodeIgniter/Perfex CRM |

### 5.2 Hiệu năng
- Trang Login tải trong ≤ 3 giây trong điều kiện mạng thông thường.
- Xử lý đăng nhập (submit → chuyển trang) ≤ 3 giây.

### 5.3 Khả dụng & tương thích
- Hỗ trợ Chrome, Firefox, Edge, Safari phiên bản mới.
- Giao diện responsive trên desktop, tablet, mobile (cần kiểm tra thực tế).
- Mỗi input có label đi kèm (hỗ trợ accessibility) **[Quan sát]**.

### 5.4 Ngôn ngữ
- Giao diện hiển thị tiếng Anh **[Quan sát]**.

---

## 6. Quy tắc nghiệp vụ

| ID | Quy tắc |
|---|---|
| BR-01 | Định danh đăng nhập là **địa chỉ email** (không dùng username). |
| BR-02 | Chỉ tài khoản staff đang hoạt động (active) mới được đăng nhập khu vực admin **[Giả định]**. |
| BR-03 | Thông báo lỗi đăng nhập dùng chung cho cả trường hợp sai email và sai mật khẩu **[Giả định]**. |

---

## 7. Luồng xử lý chính

```
Người dùng mở /admin/authentication
        │
        ▼
Hiển thị form Login (focus Email)
        │ nhập Email + Password (+ Remember me) → bấm Login (POST)
        ▼
Server kiểm tra CSRF ──sai──► Từ chối request
        │đúng
        ▼
Validate dữ liệu ──lỗi──► Hiển thị lỗi, ở lại trang Login
        │hợp lệ
        ▼
Xác thực Email/Password ──sai──► Thông báo lỗi, ở lại trang Login
        │đúng
        ▼
Tạo session (cookie nếu Remember me) → chuyển tới Dashboard
```

---

## 8. Ma trận truy vết yêu cầu → kịch bản kiểm thử gợi ý

> Các kịch bản đã thực thi thực tế được đánh dấu "✅ đã test" (xem chi tiết mục 10); các kịch bản còn lại là gợi ý cho các vòng test tiếp theo.

| Yêu cầu | Kịch bản kiểm thử gợi ý |
|---|---|
| FR-LOGIN-01 | Kiểm tra hiển thị đầy đủ thành phần, autofocus, tiêu đề tab |
| FR-LOGIN-02 | Đăng nhập với `admin@example.com` + mật khẩu đúng → vào Dashboard |
| FR-LOGIN-03 | Email đúng/mật khẩu sai; email không tồn tại; kiểm tra thông báo lỗi chung |
| FR-LOGIN-04 | Trống email, trống mật khẩu, trống cả hai, email sai định dạng, email có khoảng trắng, chuỗi rất dài, ký tự đặc biệt, SQLi/XSS payload |
| FR-LOGIN-05 | Có/không chọn Remember me → đóng/mở lại trình duyệt |
| FR-LOGIN-06 | Click Forgot Password; gửi email tồn tại / không tồn tại / sai định dạng / để trống |
| FR-LOGIN-07 | Enter để submit; thứ tự Tab |
| FR-LOGIN-08 | Truy cập `/admin` khi chưa đăng nhập; truy cập trang Login khi đã đăng nhập |
| FR-LOGIN-09 | Logout rồi bấm Back |
| NFR-SEC-02 | Gửi POST thiếu/sai CSRF token |
| NFR-SEC-06 | Đăng nhập sai liên tiếp nhiều lần, kiểm tra khóa tài khoản |
| NFR-SEC-05 | So sánh thông báo lỗi giữa email tồn tại và không tồn tại |

---

## 10. Kết quả kiểm thử thực tế & Phát hiện (Findings)

Thực hiện ngày 2026-11-09 trên `https://crm.anhtester.com/admin/authentication`, tài khoản test `admin@example.com` / `123456`.

### 10.1 Bảng kết quả theo kịch bản

| # | Kịch bản | Input | Kết quả quan sát | Đạt theo yêu cầu? |
|---|---|---|---|---|
| 1 | Trống cả hai trường | email="", password="" | "The Password field is required." + "The Email Address field is required." | Đạt |
| 2 | Trống Password | email=admin@example.com, password="" | "The Password field is required." | Đạt |
| 3 | Trống Email | email="", password=bất kỳ | "The Email Address field is required." | Đạt |
| 4 | Email sai định dạng | email="abc", password=bất kỳ | "The Email Address field must contain a valid email address." | Đạt |
| 5 | Email không tồn tại + sai password | email=nonexistent.user@example.com | "Invalid email or password" | Đạt |
| 6 | Email đúng + sai password | email=admin@example.com, password sai | "Invalid email or password" (giống #5) | Đạt – không lộ thông tin |
| 7 | Đăng nhập đúng | admin@example.com / 123456 | Redirect → `/admin/` (Dashboard) | Đạt |
| 8 | Email viết hoa | ADMIN@EXAMPLE.COM / 123456 | Đăng nhập thành công | Ghi nhận (không phân biệt hoa/thường) |
| 9 | Email có khoảng trắng | "  admin@example.com  " / 123456 | Đăng nhập thành công (tự trim) | Ghi nhận |
| 10 | Đã đăng nhập, vào lại trang Login | – | Tự chuyển về Dashboard | Đạt |
| 11 | Logout | `/admin/authentication/logout` | Chuyển về trang Login | Đạt |
| 12 | Vào `/admin/` sau khi logout | – | Chuyển về trang Login | Đạt |
| 13 | Forgot password – email tồn tại | admin@example.com | "Error setting new password" | Ghi nhận (môi trường demo có thể chưa cấu hình SMTP) |
| 14 | Forgot password – email không tồn tại | nonexistent.user@example.com | "Email not found" | **Không đạt** – xem F1 |
| 15 | Forgot password – trống email | "" | "Email not found" | Ghi nhận |
| 16 | 6 lần đăng nhập sai liên tiếp | admin@example.com / sai | Luôn "Invalid email or password", không khóa | Xem F3 |
| 17 | Remember me | checked, đăng nhập đúng | Set cookie `autologin` đọc được qua JS | Xem F2 |

### 10.2 Finding – Vấn đề phát hiện

| ID | Mức độ | Mô tả | Khuyến nghị |
|---|---|---|---|
| **F1** | Trung bình | Màn hình **Forgot Password** trả về thông báo khác nhau giữa email tồn tại ("Error setting new password") và email không tồn tại ("Email not found") → cho phép kẻ tấn công liệt kê (enumerate) email/tài khoản hợp lệ trong hệ thống. | Dùng một thông báo chung, trung lập cho cả hai trường hợp (vd: "Nếu email tồn tại trong hệ thống, hướng dẫn đặt lại mật khẩu đã được gửi."). |
| **F2** | Trung bình | Khi chọn **Remember me**, cookie `autologin` được set **không có cờ `HttpOnly`**, nội dung (user_id + key tự động đăng nhập) đọc được bằng JavaScript phía client → tăng rủi ro bị đánh cắp token qua lỗ hổng XSS (nếu có) và dùng để chiếm phiên (session hijacking/persistent login abuse). | Thêm cờ `HttpOnly` (và `Secure`, `SameSite`) cho cookie `autologin`; xem xét không lưu `user_id`/key dạng đọc được. |
| **F3** | Thấp–Trung bình (cần xác minh thêm) | Thử 6 lần đăng nhập sai liên tiếp với cùng email không ghi nhận khóa tài khoản, CAPTCHA hay độ trễ tăng dần. Giới hạn thử nghiệm ở 6 lần nên **chưa khẳng định chắc chắn** hệ thống không có ngưỡng khóa cao hơn. | Kiểm thử thêm với số lần lớn hơn (vd. 20–50 lần) trong môi trường được phép tải, hoặc xác nhận trực tiếp với đội phát triển về cấu hình "Brute Force Protection" của Perfex CRM. |

## 11. Điểm còn cần xác nhận (Open Issues)

1. Ngưỡng khóa tài khoản thực tế (nếu có) khi số lần đăng nhập sai vượt quá 6 lần (F3).
2. Hành vi gửi email thực tế của Forgot Password trên môi trường production (demo trả lỗi "Error setting new password").
3. Thời gian sống (expiry) của cookie `autologin` và session timeout khi không chọn Remember me.
4. Hành vi khi gửi request thiếu/sai `csrf_token_name` (chưa test).
5. Cơ chế 2FA có được bật cho tài khoản admin không (không thấy trên giao diện hiện tại).
6. Hành vi khi tài khoản bị vô hiệu hóa (inactive) – không có tài khoản inactive để test.
7. Kết quả kiểm thử SQL Injection/XSS trên các trường Email/Password (ngoài phạm vi khảo sát này).
