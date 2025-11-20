🚀 Hướng dẫn Deploy Next.js & NestJS lên VPS (aaPanel)
🧩 Cấu trúc ví dụ
/www/wwwroot/
├── api.rophim.nyc/       # Backend NestJS
└── rophim.nyc/           # Frontend Next.js (build sẵn)

⚙️ 1. Chuẩn bị môi trường



SSH vào VPS hoặc dùng terminal trong aaPanel:

sudo apt update
sudo apt install -y nodejs npm git
npm install -g pm2


⚠️ Nếu chạy NestJS cần Node >= 18

🧱 2. Deploy Backend (NestJS)
Bước 1: Clone code
cd /www/wwwroot/
git clone https://github.com/yourname/your-nestjs-api.git api.rophim.nyc
cd api.rophim.nyc

Bước 2: Cài đặt dependencies
npm install

Bước 3: Cấu hình biến môi trường



Tạo file .env:

PORT=4000
MONGO_URI=mongodb+srv://...
JWT_SECRET=your_secret

Bước 4: Build và chạy bằng PM2
npm run build
pm2 start dist/main.js --name "nestjs-api"
pm2 save
pm2 startup

🌐 3. Cấu hình domain cho API



Vào aaPanel → Website → Add Site

Domain: api.rophim.nyc
Path: /www/wwwroot/api.rophim.nyc
PHP version: chọn “Pure static” (vì dùng Node.js, không cần PHP)
Chỉnh file cấu hình Nginx:



Mở file /www/server/panel/vhost/nginx/api.rophim.nyc.conf và thay nội dung:

server {
    listen 80;
    server_name api.rophim.nyc;

    location / {
        proxy_pass http://127.0.0.1:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Kích hoạt SSL (HTTPS)



Vào aaPanel → Website → api.rophim.nyc → SSL → Let's Encrypt → Apply
Sau khi bật SSL, aaPanel sẽ tự thêm phần:

listen 443 ssl http2;
ssl_certificate /www/server/panel/vhost/cert/api.rophim.nyc/fullchain.pem;
ssl_certificate_key /www/server/panel/vhost/cert/api.rophim.nyc/privkey.pem;

🪄 4. Deploy Frontend (Next.js)
Bước 1: Clone code
cd /www/wwwroot/
git clone https://github.com/yourname/your-nextjs-app.git rophim.nyc
cd rophim.nyc

Bước 2: Cài đặt & build
npm install
npm run build
npm run start &


Nếu dùng PM2:

pm2 start npm --name "nextjs-web" -- start
pm2 save

🌐 5. Cấu hình domain cho Web



Vào aaPanel → Website → Add Site

Domain: rophim.nyc
Path: /www/wwwroot/rophim.nyc
PHP: Pure static



Mở file /www/server/panel/vhost/nginx/rophim.nyc.conf và thay nội dung:

server {
    listen 80;
    server_name rophim.nyc;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}




Kích hoạt SSL tương tự như API:

aaPanel → Website → rophim.nyc → SSL → Let's Encrypt → Apply
🔁 6. Reload lại Nginx



Sau khi chỉnh file cấu hình xong:

service nginx reload

🧰 7. Kiểm tra hoạt động
https://rophim.nyc → Giao diện web Next.js
https://api.rophim.nyc → API NestJS
🔄 8. Cập nhật khi thay đổi code



Khi bạn sửa code:

git pull
npm run build
pm2 restart all




Nếu có chỉnh .env thì restart lại tiến trình:

pm2 restart nestjs-api

🧹 9. Gỡ lỗi nhanh
pm2 logs nestjs-api
pm2 logs nextjs-web

✅ Kết quả cuối
Next.js chạy tại cổng 3000, trỏ domain rophim.nyc
NestJS chạy tại cổng 4000, trỏ domain api.rophim.nyc
Cả hai đều có SSL và restart tự động khi VPS khởi động lại



✍️ Tác giả: Phở
🕓 Phiên bản: 2025-10