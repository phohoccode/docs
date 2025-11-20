# 🧠 Hướng dẫn Git cơ bản - Các lệnh thường dùng

## 1. Cấu hình Git lần đầu
```bash
git config --global user.name "Tên của bạn"
git config --global user.email "email@example.com"
```
> Dùng để thiết lập tên và email cho tất cả các repo.

---

## 2. Khởi tạo Git trong thư mục
```bash
git init
```
> Khởi tạo repo Git trong thư mục hiện tại.

---

## 3. Kiểm tra trạng thái
```bash
git status
```
> Xem các thay đổi hiện tại, file nào đã được thêm, thay đổi, hoặc chưa commit.

---

## 4. Thêm file vào stage
```bash
git add tenfile.txt
git add .
```
> `add .` để thêm toàn bộ thay đổi vào stage.

---

## 5. Commit thay đổi
```bash
git commit -m "Nội dung commit"
```
> Lưu lại snapshot mã nguồn tại thời điểm đó.

---

## 6. Xem lịch sử commit
```bash
git log
```
> Hiển thị danh sách các commit theo thời gian.

---

## 7. Xem danh sách nhánh
```bash
git branch       # Hiện các nhánh local
git branch -a    # Bao gồm cả nhánh remote
git branch -r    # Chỉ các nhánh remote
```

---

## 8. Tạo và chuyển nhánh
```bash
git checkout -b ten-nhanh-moi
```
> Tạo và chuyển sang nhánh mới

```bash
git checkout ten-nhanh-cu
```
> Chuyển về nhánh đã có

---

## 9. Gộp nhánh
```bash
git checkout main
git merge ten-nhanh-phu
```
> Gộp nhánh phụ vào nhánh chính

---

## 10. Xóa nhánh
```bash
git branch -d ten-nhanh
```
> Xóa nhánh local (đã gộp rồi)

---

## 11. Liên kết đến repo từ xa (remote)
```bash
git remote add origin https://github.com/username/repo.git
```

---

## 12. Đẩy code lên GitHub
```bash
git push -u origin main
```
> Dùng `-u` để gắn nhánh local với nhánh remote

---

## 13. Kéo code từ remote về
```bash
git pull origin main
```

---

## 14. Clone repo có sẵn
```bash
git clone https://github.com/username/repo.git
```

---

## 15. Xem sự khác biệt (diff)
```bash
git diff           # So sánh thay đổi chưa stage
git diff --staged  # So sánh thay đổi đã stage
```

---

## 16. Undo một số thao tác
```bash
git restore tenfile.txt           # Hoàn tác file chưa stage
git reset HEAD tenfile.txt        # Bỏ file khỏi stage
git reset --hard HEAD             # Reset lại toàn bộ repo như lần commit cuối
```

---

## 17. Kiểm tra file bị xung đột khi merge
```bash
git status
```
> Git sẽ hiển thị các file bị xung đột cần xử lý

---

## 18. Đẩy tất cả nhánh lên remote
```bash
git push --all origin
```

---

## 19. Tag (gắn mốc phiên bản)
```bash
git tag v1.0
git push origin v1.0
```

---

## 20. Stash (giữ tạm thay đổi chưa commit)
```bash
git stash              # Lưu lại thay đổi tạm thời
git stash apply        # Áp dụng lại
git stash drop         # Xóa stash đầu tiên
```

---

📌 **Mẹo**: Dùng `git log --oneline --graph --all` để xem lịch sử nhánh dạng cây trực quan.

---

# 🚀 PHẦN MỞ RỘNG - LỆNH GIT NÂNG CAO

## 🔁 Rebase - Làm gọn lịch sử commit
```bash
git checkout main
git pull origin main
git checkout feature
git rebase main
```
> Thay vì `merge`, `rebase` giúp giữ lịch sử commit sạch sẽ và tuyến tính hơn.

---

## 🔍 Bisect - Tìm commit gây lỗi
```bash
git bisect start
git bisect bad          # Commit hiện tại bị lỗi
git bisect good abc123  # Commit trước đó vẫn hoạt động tốt
```
> Git sẽ tự động giúp bạn kiểm tra từng commit để tìm ra commit gây lỗi.

---

## 🎯 Cherry-pick - Lấy commit cụ thể từ nhánh khác
```bash
git cherry-pick commit_id
```
> Dùng khi bạn muốn lấy 1 commit từ nhánh khác và áp dụng vào nhánh hiện tại.

---

## 🧹 Dọn dẹp file chưa được theo dõi (Untracked)
```bash
git clean -fd
```
- `-f`: buộc xóa
- `-d`: xóa cả thư mục chưa được theo dõi

---

## 🔐 Remote nâng cao - Làm việc với nhiều nguồn
```bash
git remote add upstream https://github.com/nguon-goc/repo.git
git fetch upstream
git merge upstream/main
```
> Dùng khi bạn fork repo và muốn cập nhật từ repo gốc.

---

## 📌 Git Flow (làm việc nhóm chuẩn)
Các nhánh thường có:
- `main`: phiên bản chạy thật
- `develop`: nhánh phát triển chung
- `feature/*`: nhánh tính năng
- `release/*`: chuẩn bị bản phát hành
- `hotfix/*`: sửa lỗi nhanh

Cài `git-flow` nếu muốn dùng CLI:
```bash
brew install git-flow   # hoặc sudo apt install git-flow
git flow init
```

---

## 🔀 GitHub Flow (đơn giản hơn Git Flow)
1. `main` luôn deploy được
2. Tạo nhánh feature: `git checkout -b feature/ten`
3. Commit, push, mở Pull Request
4. Code review → merge vào `main`
5. Triển khai

---

## 🗂️ Lưu alias Git cho nhanh hơn
```bash
git config --global alias.st status
git config --global alias.ci commit
git config --global alias.co checkout
git config --global alias.br branch
```
> Giúp bạn dùng lệnh ngắn như `git st`, `git ci -m "msg"`

---

## 📈 Xem biểu đồ lịch sử commit dạng cây
```bash
git log --oneline --graph --all --decorate
```

---

## 🔐 SSH Key để push không cần mật khẩu (GitHub)
1. Tạo SSH key:
```bash
ssh-keygen -t ed25519 -C "you@example.com"
```
2. Copy key:
```bash
cat ~/.ssh/id_ed25519.pub
```
3. Dán lên GitHub → Settings → SSH and GPG Keys

---

## 🔁 Force Push (thận trọng!)
```bash
git push --force
```
> Ghi đè lịch sử trên remote. **Chỉ dùng khi chắc chắn.**

---

## ☁️ Làm việc với Submodule (repo lồng repo)
```bash
git submodule add https://github.com/example/lib.git lib/
git submodule update --init --recursive
```

---

👉 Đây là bộ mở rộng nâng cao giúp bạn sử dụng Git hiệu quả trong cả môi trường cá nhân lẫn team chuyên nghiệp.