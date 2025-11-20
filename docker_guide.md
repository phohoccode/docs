
# 📦 Hướng dẫn Docker cơ bản cho dự án Node.js, Next.js, Express, MySQL, MongoDB

## ✅ Cài đặt Docker
- Tải và cài Docker Desktop: https://www.docker.com/products/docker-desktop/

---

## 🔹 1. Các lệnh Docker cơ bản

### 🛠 Build image từ Dockerfile
```bash
docker build -t ten-image .
```
Ví dụ:
```bash
docker build -t my-nextjs-app ./frontend
docker build -t my-express-api ./backend
```

### 🖼 Xem danh sách image
```bash
docker images
```

### 🚀 Chạy container từ image
```bash
docker run -p <port-host>:<port-container> ten-image
```
Ví dụ:
```bash
docker run -p 3000:3000 my-nextjs-app
```

### ❌ Xóa container đang chạy
```bash
docker ps           # Liệt kê container
docker stop <container-id>
docker rm <container-id>
```

### ❌ Xóa image
```bash
docker rmi <image-id>
```

### 🔁 Gắn nhãn (tag) image
```bash
docker tag ten-image ten-user/ten-image
```

### ☁️ Đăng nhập Docker Hub
```bash
docker login
```

### ☁️ Push image lên Docker Hub
```bash
docker push ten-user/ten-image
```

### ☁️ Pull image từ Docker Hub
```bash
docker pull ten-user/ten-image
```

---

## 🧩 2. Docker Compose

### 🚀 Khởi chạy nhiều container cùng lúc
```bash
docker-compose up
```

### 🛠 Build lại toàn bộ container
```bash
docker-compose build
```

### ❌ Dừng tất cả container
```bash
docker-compose down
```

### 📁 Cấu trúc ví dụ
```bash
project-root/
├── frontend/           # Next.js
│   └── Dockerfile
├── backend/            # Express.js
│   └── Dockerfile
├── docker-compose.yml
```

---

## 📄 3. Ví dụ `docker-compose.yml`

```yaml
version: '3.9'

services:
  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD: pass
    ports:
      - "3306:3306"

  mongo:
    image: mongo
    ports:
      - "27017:27017"

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    depends_on:
      - mysql
      - mongo

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
```

---

## 📌 Ghi nhớ
| Lệnh | Mục đích |
|------|----------|
| `docker build` | Tạo image |
| `docker run`   | Chạy container |
| `docker ps`    | Kiểm tra container đang chạy |
| `docker push`  | Đẩy image lên Docker Hub |
| `docker-compose up` | Khởi động toàn bộ hệ thống |

---

> **Tác giả:** ChatGPT + Quốc Việt – 2025
