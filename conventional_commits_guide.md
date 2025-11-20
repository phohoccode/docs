# 📘 Hướng dẫn chi tiết Conventional Commits

Conventional Commits là một quy ước đặt tên commit message để dễ đọc, tự động sinh changelog, hỗ trợ CI/CD và semantic versioning.

---

## 🧱 Cấu trúc commit message

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

### Ví dụ:

```
feat(auth): thêm chức năng đăng nhập bằng Google

fix(api): sửa lỗi không trả về dữ liệu khi query rỗng

docs(readme): cập nhật hướng dẫn cài đặt
```

---

## 🏷️ Các `type` phổ biến

| Type       | Mô tả |
|------------|-------|
| `feat`     | Thêm tính năng mới |
| `fix`      | Sửa lỗi |
| `docs`     | Thay đổi tài liệu |
| `style`    | Định dạng code (không thay đổi logic) |
| `refactor` | Refactor code (không thêm tính năng, không sửa lỗi) |
| `perf`     | Tối ưu hiệu năng |
| `test`     | Thêm hoặc cập nhật test |
| `chore`    | Thay đổi phụ trợ (config, build tool, v.v) |
| `revert`   | Hoàn tác commit trước đó |

---

## 🧠 Quy tắc viết tốt

- Không viết hoa `type`
- Không có dấu chấm ở cuối dòng đầu
- Viết mô tả bằng câu lệnh ngắn gọn, chủ động (thêm, sửa, cập nhật...)
- Dòng đầu nên ngắn gọn dưới 72 ký tự

---

## 🛠️ Công cụ hỗ trợ

- **[Commitizen](https://github.com/commitizen/cz-cli)**: tạo commit theo chuẩn Conventional Commit.
- **[Commitlint](https://github.com/conventional-changelog/commitlint)**: kiểm tra message có đúng format không.
- **[Husky](https://github.com/typicode/husky)**: hook kiểm tra format trước khi commit.

---

## 📌 Lợi ích

- Tự động tạo changelog (với semantic-release)
- Tự động tăng version (patch, minor, major)
- Dễ review code, dễ hiểu lịch sử commit
- Hỗ trợ CI/CD rõ ràng

---

## 📚 Tham khảo

- Trang chủ: https://www.conventionalcommits.org/en/v1.0.0/
- Semantic release: https://semantic-release.gitbook.io