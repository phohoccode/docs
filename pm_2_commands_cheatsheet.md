# PM2 Command Cheat Sheet

PM2 là trình quản lý tiến trình Node.js giúp chạy, giám sát và quản lý các ứng dụng Node.js ở môi trường production.

---

## 1. Cài đặt PM2
```bash
npm install pm2 -g
```

## 2. Khởi chạy ứng dụng
```bash
pm2 start app.js               # Khởi chạy app.js
pm2 start app.js --name my-app # Khởi chạy với tên process
```
Với project Node có script start:
```bash
pm2 start npm --name "my-app" -- start
```

## 3. Quản lý process
```bash
pm2 list          # Danh sách process
pm2 logs          # Log realtime của tất cả process
pm2 logs my-app   # Log của process my-app
pm2 info my-app   # Thông tin chi tiết process
```

## 4. Quản lý process
```bash
pm2 stop my-app        # Dừng process
pm2 restart my-app     # Khởi động lại process
pm2 delete my-app      # Xóa process khỏi PM2
pm2 restart all        # Khởi động lại tất cả process
pm2 stop all           # Dừng tất cả process
```

## 5. Tự động chạy khi server khởi động
```bash
pm2 startup   # Tạo script startup
pm2 save      # Lưu trạng thái process hiện tại
```

## 6. Quản lý log
```bash
pm2 logs             # Xem log realtime
pm2 logs --err       # Xem log lỗi
pm2 logs --out       # Xem log output
```

## 7. Lệnh nâng cao
```bash
pm2 reload my-app      # Reload process không downtime (cluster mode)
pm2 start app.js -i max # Cluster mode, tối ưu CPU
pm2 reset all          # Reset tất cả stats
pm2 kill               # Xóa tất cả process và log
```

---

> Lưu ý: `-i max` trong cluster mode sẽ tạo process bằng số lượng core CPU hiện tại, giúp tối ưu hiệu năng.

