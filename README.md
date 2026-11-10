# AI Testing Manual

Tài liệu minh họa quy trình kiểm thử phần mềm có hỗ trợ của AI: từ khảo sát một màn hình/chức năng thực tế, viết **SRS (Software Requirements Specification)**, đến thiết kế và **thực thi Test Case**, ghi nhận kết quả cùng các phát hiện (findings) về chất lượng/bảo mật.

## Mục tiêu

- Làm mẫu cách dùng AI (Claude Code) để:
  1. Khảo sát giao diện/hành vi thực tế của một chức năng trên hệ thống đang chạy.
  2. Tổng hợp thành tài liệu SRS có phân loại rõ nguồn thông tin (quan sát thực tế vs. giả định).
  3. Từ SRS, thiết kế bộ Test Case và **thực thi trực tiếp** trên hệ thống để lấy kết quả thật (không chỉ viết case lý thuyết).
  4. Ghi nhận lỗi/rủi ro phát hiện được (bug, lỗ hổng bảo mật) kèm mức độ và khuyến nghị xử lý.

## Cấu trúc thư mục

```
.
├── LICENSE
├── README.md
└── docs/
    ├── SRS_Login_Module.md        # Đặc tả yêu cầu cho module Đăng nhập (Login)
    └── TestCase_Login_Module.md   # Bộ test case + kết quả thực thi cho module Đăng nhập
```

## Module đã thực hiện: Đăng nhập (Login)

| Tài liệu | Nội dung |
|---|---|
| [docs/SRS_Login_Module.md](docs/SRS_Login_Module.md) | Đặc tả giao diện, yêu cầu chức năng/phi chức năng, quy tắc nghiệp vụ, luồng xử lý và các phát hiện bảo mật của màn hình Login (Perfex CRM – Anh Tester Demo). |
| [docs/TestCase_Login_Module.md](docs/TestCase_Login_Module.md) | 34 test case được thực thi thực tế trên demo, kèm kết quả Đạt/Không đạt/Ghi nhận và liên kết tới các finding bảo mật tương ứng. |

Hệ thống dùng để khảo sát/kiểm thử: `https://crm.anhtester.com/admin/authentication` (Perfex CRM demo).

> **Lưu ý:** Tài khoản, URL và dữ liệu trong các tài liệu trên chỉ phục vụ mục đích học tập/demo trên môi trường test công khai, không chứa thông tin thật của bất kỳ hệ thống sản xuất nào.

## Quy ước tài liệu

Các tài liệu trong `docs/` đánh dấu rõ nguồn gốc thông tin để người đọc biết mức độ tin cậy:

- **[Quan sát]** – xác nhận trực tiếp từ hệ thống (giao diện, kết quả submit form thực tế).
- **[Giả định]** – suy luận theo hành vi chuẩn của nền tảng, chưa kiểm thử trực tiếp để xác nhận.
- ✅ Đạt / ❌ Không đạt / ℹ️ Ghi nhận / ⛔ Chưa thực thi – trạng thái của từng test case.

## License

Phát hành theo [MIT License](LICENSE).
