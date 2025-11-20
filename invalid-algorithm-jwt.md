
# Giải thích lỗi `invalid algorithm` (JWT) — chi tiết & cách khắc phục

**Mô tả ngắn:**\
Lỗi `invalid algorithm` thường xuất hiện khi quá trình **kiểm tra (verify)** một JWT gặp sự không khớp giữa **thuật toán** (trong header `alg`) của token và **thuật toán / loại khoá** mà phía server (hoặc thư viện) dùng để xác thực token. Dưới đây là nguyên nhân, cách debug và cách sửa chi tiết.

---

## 1. Nguyên nhân phổ biến

1. **`alg` trong header token ≠ thuật toán mà verifier mong đợi**  
   - Ví dụ token có header `alg: "RS256"` nhưng code verify dùng secret cho `HS256` (hoặc verify được cấu hình chỉ chấp nhận `HS256`).

2. **Sai kiểu khoá (key type mismatch)**  
   - `HSxxx` (ví dụ `HS256`) dùng **symmetric secret** (một chuỗi bí mật chung).  
   - `RSxxx` (ví dụ `RS256`) dùng **asymmetric keypair** (private key để sign, public key để verify). Dùng secret HS để verify token RS sẽ không đúng.

3. **Môi trường local và production cấu hình khác nhau**  
   - Ở local bạn có thể sign token bằng HS256, nhưng ở production có thể sign bằng RS256 (do environment variables, thư viện, hoặc default behavior khác).

4. **Key/PEM format không đúng**  
   - Thiếu header/footer `-----BEGIN PRIVATE KEY-----` / `-----END PRIVATE KEY-----`, hoặc bị trộn newline, hoặc bị base64 encode nhầm — khiến verify thất bại.

5. **Thư viện hoặc framework tự đổi thuật toán khi thiếu cấu hình**  
   - Một số framework (hoặc NextAuth/Auth.js nếu không cấu hình encode/decode) có thể fallback hoặc dùng thuật toán khác trong production.

6. **Token bị giả mạo hoặc header `alg` bị thay đổi**  
   - Nếu attacker thay `alg` sang `none` hoặc đổi algorithm, và server không kiểm tra chặt chẽ, sẽ gây lỗi/nhược điểm bảo mật.

---

## 2. Cách debug nhanh (bắt đầu từ token bạn có)

1. **Xem header của token** (không cần secret):\
   ```js
   const jwt = require('jsonwebtoken');
   const decoded = jwt.decode(token, { complete: true });
   console.log(decoded.header); // xem alg, typ ...
   ```
   Hoặc thủ công CLI:
   ```bash
   # lấy phần header (chuỗi base64) và decode
   echo 'TOKEN' | awk -F'.' '{print $1}' | base64 --decode
   ```

2. **So sánh `alg` với config của verifier**: verifier (jwt.verify) có option `algorithms`. Kiểm tra xem bạn chỉ cho phép `['HS256']` nhưng token `alg` là `RS256`.

3. **Kiểm tra loại khoá**: nếu token `alg` là `RS256`, bạn cần **public key** (PEM) để verify. Nếu `HS256`, bạn cần **secret string**.

4. **Kiểm tra biến môi trường/ cấu hình production** (private/public key hoặc secret có đúng và đã load không?).

5. **Khởi tạo lại token trên môi trường local với cùng config production** — thử reproduce lỗi để thấy nguyên nhân.

---

## 3. Cách sửa (tùy trường hợp)

### A. Nếu muốn dùng HS256 (đơn giản, shared secret)
- **Sign (issuer):**
  ```js
  const jwt = require('jsonwebtoken');
  const token = jwt.sign({ sub: 'userId' }, process.env.JWT_SECRET, {
    algorithm: 'HS256',
    expiresIn: '1h',
  });
  ```
- **Verify (consumer):**
  ```js
  jwt.verify(token, process.env.JWT_SECRET, { algorithms: ['HS256'] });
  ```
- **Lưu ý:** giữ `JWT_SECRET` an toàn (env var), cả issuer và verifier phải dùng cùng biến môi trường / cùng giá trị.

### B. Nếu muốn dùng RS256 (khuyến nghị khi cần tách private/public)
- **Tạo keypair:**\
  ```bash
  openssl genpkey -algorithm RSA -out private.pem -pkeyopt rsa_keygen_bits:2048
  openssl rsa -pubout -in private.pem -out public.pem
  ```
- **Sign (dùng private key):**
  ```js
  const privateKey = fs.readFileSync('./private.pem', 'utf8');
  const token = jwt.sign({ sub: 'userId' }, privateKey, {
    algorithm: 'RS256',
    expiresIn: '1h',
  });
  ```
- **Verify (dùng public key):**
  ```js
  const publicKey = fs.readFileSync('./public.pem', 'utf8');
  jwt.verify(token, publicKey, { algorithms: ['RS256'] });
  ```
- **Lưu ý:** private key chỉ ở server sign; public key có thể phân phối cho các service verify.

### C. Nếu dùng framework như Auth.js / NextAuth
- Nếu bạn **override** `encode`/`decode` (như code bạn đưa: ép HS256), **phải** ensure override đó tồn tại cả ở production. Nếu production không load override, framework có thể dùng default algorithm khác.
- Kiểm tra biến môi trường cần thiết (ví dụ `NEXTAUTH_SECRET`, `NEXTAUTH_JWT_SECRET` hoặc tương tự tùy framework). Thiếu secret có thể khiến behavior khác.
- Luôn kiểm tra log khi sign/verify để biết thuật toán đang được dùng (đặt `console.log` hoặc debug).

---

## 4. Vấn đề format / pitfalls thường gặp

- **Khoá RSA mất header/footer**: khi đọc từ env var, nếu key được lưu trong một dòng, bạn cần phục hồi newline `\n` hoặc dùng chuỗi multiline. Ví dụ trong `.env`:
  ```text
  PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANBgkq...\n-----END PRIVATE KEY-----"
  ```
- **CRLF vs LF**: format newline khác nhau có thể gây lỗi. Hãy chắc là key đúng định dạng PEM.  
- **Không dùng `secret` HS để verify RS token** — sẽ luôn sai.  
- **Không cho phép `algorithms` rộng quá** (ví dụ `['HS256','RS256']`) trừ khi bạn thực sự cần hỗ trợ nhiều thuật toán — vì điều đó có thể mở cửa cho các tấn công nếu không kiểm soát chặt chẽ.

---

## 5. Mã mẫu kiểm tra nhanh (Node.js)

**Decode header:**
```js
const jwt = require('jsonwebtoken');
const token = 'eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...';

console.log(jwt.decode(token, { complete: true }).header);
/*
  { alg: 'RS256', typ: 'JWT' }
*/
```

**Test sign/verify HS256:**
```js
const jwt = require('jsonwebtoken');
const secret = 'my-secret-123';

const t = jwt.sign({ sub: 'u1' }, secret, { algorithm: 'HS256', expiresIn: '1h' });
console.log('t', t);
jwt.verify(t, secret, { algorithms: ['HS256'] });
console.log('HS256 OK');
```

**Test sign/verify RS256:**
```js
const jwt = require('jsonwebtoken');
const fs = require('fs');
const privateKey = fs.readFileSync('./private.pem', 'utf8');
const publicKey = fs.readFileSync('./public.pem', 'utf8');

const t = jwt.sign({ sub: 'u1' }, privateKey, { algorithm: 'RS256', expiresIn: '1h' });
console.log('t', t);
jwt.verify(t, publicKey, { algorithms: ['RS256'] });
console.log('RS256 OK');
```

---

## 6. Checklist sửa lỗi `invalid algorithm` nhanh

1. `jwt.decode(token, { complete: true })` → xem `alg`.  
2. So sánh với code sign/verify: có cùng thuật toán không?  
3. Với HS256: cả 2 bên dùng **cùng SECRET**.  
4. Với RS256: bên sign dùng **private key**, bên verify dùng **public key** (đúng định dạng PEM).  
5. Kiểm tra biến môi trường production (SECRET / PRIVATE_KEY / PUBLIC_KEY).  
6. Nếu dùng Auth.js/NextAuth: kiểm tra `encode/decode` nếu override.  
7. Bật thêm logging tạm thời để in thuật toán khi sign/verify.  
8. Nếu cần tạm thời hỗ trợ cả 2 thuật toán, chỉ làm khi biết rõ rủi ro và whitelist `algorithms` một cách an toàn.

---

## 7. Kết luận ngắn gọn
Lỗi `invalid algorithm` **không phải** là lỗi mã hóa bí ẩn — nó chỉ ra rằng **thuật toán hoặc loại khoá** giữa bên phát và bên kiểm tra không khớp. Giải pháp thường là kiểm tra header token (`alg`), đảm bảo cấu hình sign/verify dùng cùng loại (HS vs RS), và kiểm tra định dạng/giá trị của secret / PEM key trong môi trường production.

---

## 8. Tham khảo nhanh
- Tài liệu `jsonwebtoken` (node): cách `sign`, `verify`, option `algorithms`.
- Hướng dẫn tạo RSA keypair: `openssl genpkey` + `openssl rsa -pubout`.
(Ở đây không chèn link trực tiếp; nếu bạn muốn mình có thể kèm link chính thức.)

---
