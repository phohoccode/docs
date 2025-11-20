
# 📦 Tổng hợp & Giải thích Chi tiết các Câu lệnh Docker

## 1. Image Commands (Làm việc với Docker Image)

### `docker images`
Hiển thị danh sách các Docker image đã được tải về máy.

### `docker pull <image-name>`
Tải một image từ Docker Hub về local.
- Ví dụ: `docker pull node:18` sẽ tải image Node.js phiên bản 18.

### `docker rmi <image-id>`
Xóa một image khỏi local bằng ID hoặc tên.

### `docker image prune -a`
Xóa tất cả các image không còn được sử dụng (dangling images).

---

## 2. Container Commands (Làm việc với Container)

### `docker ps`
Liệt kê các container **đang chạy**.

### `docker ps -a`
Hiển thị tất cả container, bao gồm cả đã dừng.

### `docker run -it --name <container-name> <image-name>`
Tạo và chạy một container mới với terminal tương tác.
- `-it`: Gắn terminal tương tác (interactive + TTY).
- `--name`: Gán tên cho container để dễ quản lý.

### `docker run -p <host-port>:<container-port> <image-name>`
Chạy container và ánh xạ cổng từ máy chủ tới container.
- Ví dụ: `-p 8080:3000` ánh xạ cổng 3000 trong container ra cổng 8080 máy chủ.

### `docker run -v <host-path>:<container-path> <image-name>`
Mount volume: ánh xạ thư mục giữa máy chủ và container.
- Ví dụ: `-v $(pwd):/app` chia sẻ thư mục hiện tại vào `/app` trong container.

### `docker start <container-name>`
Khởi động lại một container đã từng dừng trước đó.

### `docker stop <container-name>`
Dừng container đang chạy.

### `docker rm <container-name>`
Xóa container khỏi hệ thống.

---

## 3. Dockerfile & Build Image

### `docker build -t <image-name> .`
Build Docker image từ Dockerfile trong thư mục hiện tại (`.`).
- `-t`: Gán tên cho image.

### `docker build -t <username>/<image-name>:<tag> .`
Build image và gắn tên + tag để tiện push lên Docker Hub.

---

## 4. Docker Compose (Quản lý nhiều container)

### `docker-compose up`
Khởi chạy tất cả container được định nghĩa trong file `docker-compose.yml`.

### `docker-compose build --no-cache`
  

### `docker-compose up -d`
Khởi chạy docker-compose ở chế độ nền (background, detached mode).

### `docker-compose down`
Dừng và xóa toàn bộ container, network, volumes đã tạo bởi compose.

### `docker-compose logs -f`
Xem log thời gian thực từ các container trong docker-compose.

---

## 5. Khác

### `docker logs <container-name>`
Xem log của container đang hoặc đã chạy.

### `docker exec -it <container-name> /bin/bash`
Truy cập vào bên trong container đang chạy với shell bash (giống như SSH).

### `docker push <username>/<image-name>:<tag>`
Đẩy image từ local lên Docker Hub.

### `docker login`
Đăng nhập vào Docker Hub bằng tài khoản cá nhân.

### `docker logout`
Đăng xuất khỏi Docker Hub.

### `docker container prune`
Xóa tất cả container **đã dừng**, giải phóng tài nguyên hệ thống.

---

## 📌 Mẹo:

- Dùng `docker system prune` để dọn sạch tất cả: image, container, volume, network không còn dùng.
- Gán tag rõ ràng (`latest`, `v1.0.0`,...) giúp quản lý version dễ hơn.
- Luôn dùng `docker-compose` khi dự án có nhiều service: frontend, backend, db...
