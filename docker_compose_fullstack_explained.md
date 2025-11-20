# Giải thích các thành phần trong `docker-compose.yml` cho ứng dụng Fullstack

## 1. `version`
- Chỉ định phiên bản cú pháp Docker Compose.
- Ví dụ: 
```yaml
version: "3.9"
```

---

## 2. `services`
- Khai báo danh sách các container sẽ chạy.
- Mỗi **service** đại diện cho một container.
- Ví dụ:
```yaml
services:
  backend:
    build: ./backend
    ports:
      - "5000:5000"
```

---

## 3. Các thành phần trong một service

| Thành phần         | Chức năng |
|--------------------|-----------|
| **`image`**        | Chỉ định image từ Docker Hub hoặc registry khác để chạy container. |
| **`build`**        | Chỉ đường dẫn tới thư mục chứa `Dockerfile` để build image từ code của bạn. |
| **`container_name`** | Đặt tên cho container, dễ quản lý hơn khi chạy `docker ps`. |
| **`ports`**        | Map cổng của máy host sang cổng của container (`host:container`). |
| **`environment`**  | Khai báo biến môi trường trực tiếp trong file. |
| **`env_file`**     | Chỉ định file `.env` chứa biến môi trường. |
| **`volumes`**      | Gắn thư mục/ổ đĩa giữa host và container để dữ liệu không bị mất khi container dừng. |
| **`depends_on`**   | Xác định thứ tự khởi chạy container, service A chạy sau khi service B sẵn sàng. |
| **`restart`**      | Chính sách khởi động lại (`always`, `on-failure`, `unless-stopped`). |
| **`command`**      | Ghi đè lệnh mặc định khi container chạy. |

---

## 4. `volumes` (Global)
- Khai báo ổ lưu trữ chia sẻ cho nhiều container.
- Ví dụ:
```yaml
volumes:
  mysql_data:
  mongo_data:
```

---

## 5. `networks` (tùy chọn)
- Tạo mạng riêng cho các container giao tiếp với nhau.
- Ví dụ:
```yaml
networks:
  app_network:
    driver: bridge
```

---

## 6. Ví dụ cho ứng dụng Fullstack (Next.js + Express + MySQL + MongoDB)

```yaml
version: "3.9"

services:
  backend:
    build: ./backend
    container_name: backend_service
    ports:
      - "5000:5000"
    env_file:
      - ./backend/.env
    depends_on:
      - mysql
      - mongodb

  frontend:
    build: ./frontend
    container_name: frontend_service
    ports:
      - "3000:3000"
    env_file:
      - ./frontend/.env
    depends_on:
      - backend

  mysql:
    image: mysql:8.0
    container_name: mysql_service
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: myappdb
      MYSQL_USER: appuser
      MYSQL_PASSWORD: apppassword
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  mongodb:
    image: mongo:6.0
    container_name: mongo_service
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mysql_data:
  mongo_data:
```

---

## 7. Quy trình hoạt động
1. **MySQL** và **MongoDB** khởi chạy trước (theo `depends_on`).
2. **Backend** (Express) kết nối tới MySQL và MongoDB.
3. **Frontend** (Next.js) kết nối tới Backend.
4. Tất cả container giao tiếp qua mạng nội bộ Docker.
