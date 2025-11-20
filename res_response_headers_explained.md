# Giải thích chi tiết các thuộc tính và header trên `res` (Response) trong Node.js / Express / NestJS

Tài liệu này giải thích chi tiết các **phương thức**, **thuộc tính**, và **header** bạn thường thao tác trên object `res` (response) khi trả file, stream, hoặc dữ liệu trong ứng dụng Node.js/Express và NestJS.

> Ngữ cảnh: `res` là đối tượng Response của Express (hoặc của NestJS khi dùng `@Res()`), dùng để cấu hình header, status, body, streaming, redirect, v.v.

---

## Mở đầu — Các phương thức thường dùng

- `res.set(headers)` hoặc `res.header(name, value)`
  - Dùng để gán nhiều header cùng lúc hoặc từng header. `res.set` là alias trong Express.
  - Ví dụ: `res.set({ 'Content-Type': 'video/mp4' })`.

- `res.type(type)`
  - Thiết lập `Content-Type` dựa trên `mime` (ví dụ: `res.type('png')` => `image/png`).

- `res.status(code)`
  - Đặt HTTP status code (200, 404, 206, 500, ...).

- `res.send(body)`
  - Gửi body (string, Buffer, object). Nếu là object thì Express tự chuyển sang JSON và set `Content-Type: application/json`.

- `res.json(obj)`
  - Gửi JSON (tương tự `res.send` với object nhưng an toàn hơn về mặt headers).

- `res.sendFile(path, options, callback)`
  - Gửi file từ ổ đĩa (tự set `Content-Type` dựa trên extension).

- `res.download(path, filename?, options?, callback?)`
  - Gọi `Content-Disposition: attachment; filename="..."` để trình duyệt tải file.

- `res.attachment(filename)`
  - Gán header để download (chỉ header, không gửi body).

- `res.redirect(url)`
  - Gửi redirect (3xx) và set `Location` header.

- `res.setHeader(name, value)` / `res.getHeader(name)`
  - API lower-level (Node.js) để thao tác header.

- `res.flushHeaders()`
  - Gửi header xuống client ngay lập tức (hữu ích khi streaming từng phần).

---

## Các header quan trọng (chi tiết)

### `Content-Type`
- Mô tả MIME type của body. **Phải có dạng `type/subtype`**.
- Ví dụ: `image/png`, `image/jpeg`, `video/mp4`, `application/json`.
- Nếu chỉ ghi `image` thì **không hợp lệ**.
- `res.type('png')` tương đương `res.set('Content-Type', 'image/png')`.

### `Content-Disposition`
- Kiểm soát cách trình duyệt xử lý file: hiển thị hay tải về.
  - `inline` → hiển thị trong trình duyệt nếu có thể.
  - `attachment; filename="name.ext"` → bắt trình duyệt tải file về.

Ví dụ:
```js
res.set({
  'Content-Type': 'video/mp4',
  'Content-Disposition': 'inline'
});
```

### `Content-Length`
- Chiều dài (bytes) của body được gửi. Nếu biết trước, bạn nên set để client biết kích thước.
- Với streaming (stream) bạn thường không set `Content-Length` trừ khi có thể tính được.

### `Transfer-Encoding`
- `chunked` là mặc định khi server stream dữ liệu mà không biết `Content-Length` trước.

### `Accept-Ranges` / `Content-Range` (Range Requests)
- Dùng để hỗ trợ **resume** hoặc **seek** video/audio. Quan trọng khi stream video.
- `Accept-Ranges: bytes` báo client rằng server hỗ trợ Range requests.
- `Content-Range: bytes START-END/TOTAL` dùng khi trả partial content (status code `206`).

Ví dụ xử lý range cho video:
```js
const range = req.headers.range;
if (!range) {
  // trả toàn bộ
  res.setHeader('Content-Length', stat.size);
  res.status(200).sendFile(filePath);
} else {
  // parse range, trả partial
  res.status(206);
  res.set({
    'Content-Range': `bytes ${start}-${end}/${stat.size}`,
    'Accept-Ranges': 'bytes',
    'Content-Length': chunkSize,
    'Content-Type': 'video/mp4'
  });
  // pipe stream từ start tới end
}
```

### `Content-Encoding`
- Nếu server nén dữ liệu (gzip, br, deflate).
- Client cần biết để giải nén.

### `Vary`
- Dùng khi cache: ví dụ `Vary: Accept-Encoding` nghĩa là bản cached khác nhau tùy `Accept-Encoding` header.

### `Cache-Control`, `Expires`, `ETag`, `Last-Modified`
- Cache-Control: chỉ định chính sách cache (vd: `no-cache`, `max-age=3600`, `public`, `private`).
- ETag: hash đại diện cho phiên bản file (dùng để conditional requests `If-None-Match`).
- Last-Modified / If-Modified-Since: conditional GET.

### `Set-Cookie` / Cookie headers
- Dùng để set cookie. Có thể kèm các flags: `HttpOnly`, `Secure`, `SameSite=strict|lax|none`.

### `Location`
- Dùng với redirect (3xx). `res.redirect('/login')` sẽ set `Location`.

### CORS headers (quan trọng khi client khác nguồn)
- `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, `Access-Control-Allow-Credentials`.
- Lưu ý: CORS phải được cấu hình đúng phía server, thường dùng middleware `cors()`.

### Security headers (khuyến nghị)
- `X-Content-Type-Options: nosniff` → ngăn trình duyệt tự đoán MIME type.
- `X-Frame-Options: DENY|SAMEORIGIN` → tránh clickjacking.
- `Content-Security-Policy` → hạn chế nguồn tải script/style/img.
- `Strict-Transport-Security` → ép dùng HTTPS (HSTS).

---

## Streaming file (video/audio) — ví dụ đầy đủ cho video MP4 (range support)

```js
// Express example
app.get('/video', (req, res) => {
  const filePath = path.join(__dirname, 'videos', 'big.mp4');
  const stat = fs.statSync(filePath);
  const fileSize = stat.size;
  const range = req.headers.range;

  if (range) {
    const parts = range.replace(/bytes=/, '').split('-');
    const start = parseInt(parts[0], 10);
    const end = parts[1] ? parseInt(parts[1], 10) : fileSize - 1;
    const chunkSize = (end - start) + 1;

    const file = fs.createReadStream(filePath, { start, end });
    res.writeHead(206, {
      'Content-Range': `bytes ${start}-${end}/${fileSize}`,
      'Accept-Ranges': 'bytes',
      'Content-Length': chunkSize,
      'Content-Type': 'video/mp4'
    });
    file.pipe(res);
  } else {
    res.writeHead(200, {
      'Content-Length': fileSize,
      'Content-Type': 'video/mp4'
    });
    fs.createReadStream(filePath).pipe(res);
  }
});
```

**Giải thích:**
- Khi `req.headers.range` tồn tại, client (thường là player) yêu cầu một phần (range) của file để seek / resume.
- Server phải trả `206 Partial Content` và set `Content-Range` + `Accept-Ranges`.
- Nếu trả toàn bộ, trả `200` và có thể set `Content-Length`.

---

## Ví dụ NestJS với `StreamableFile` và custom headers

```ts
import { Controller, Get, Res } from '@nestjs/common';
import { createReadStream } from 'fs';
import { join } from 'path';
import { StreamableFile } from '@nestjs/common';

@Controller('media')
export class MediaController {
  @Get('sample')
  getSample(@Res({ passthrough: true }) res) {
    const file = createReadStream(join(process.cwd(), 'uploads', 'sample.mp4'));
    res.set({ 'Content-Type': 'video/mp4', 'Content-Disposition': 'inline' });
    return new StreamableFile(file);
  }
}
```

**Lưu ý:**
- `@Res({ passthrough: true })` cho phép NestJS tiếp tục xử lý return value (không bắt buộc gọi `res.send()` thủ công). Nếu bạn không dùng `passthrough`, NestJS coi controller là fully manual (bạn phải gọi `res.send()` và không return giá trị để framework auto handle).
- Nếu cần xử lý Range requests trong NestJS, bạn thường phải dùng `@Req()` để đọc `range` và stream bằng `createReadStream(start,end)`.

---

## Lưu ý & best practices
- **Luôn set `Content-Type` chính xác.** Nếu biết subtype cụ thể (image/png, video/mp4), hãy set chính xác.
- **Dùng `Content-Disposition` phù hợp**: `inline` để hiển thị, `attachment` để tải.
- **Hỗ trợ range requests** cho video/audio lớn nếu app của bạn stream media.
- **Không đọc toàn bộ file vào memory** (buffer) với file lớn — dùng stream (`fs.createReadStream`) để giảm dùng RAM.
- **Thiết lập security headers** cơ bản (CSP, X-Content-Type-Options, HSTS, X-Frame-Options).
- **Cân nhắc cache**: set `Cache-Control` / `ETag` để cải thiện hiệu năng tải lại.

---

## Kết luận ngắn
- `res.set({...})` là cách gán header nhanh khi trả file.
- `Content-Type` phải là `type/subtype`. `image` không đủ.
- Để stream video tốt, cần `Accept-Ranges`, `Content-Range` và trả `206` khi partial.
- Trong NestJS, `StreamableFile` + `@Res({ passthrough: true })` giúp trả file thoải mái, nhưng xử lý range thường cần streaming thủ công.

---

Nếu bạn muốn, mình có thể:
- Viết ví dụ NestJS hoàn chỉnh xử lý range requests cho video (controller + pipe).
- Hoặc tạo checklist header tối ưu cho production (CSP, HSTS, caching).

Bạn muốn mình bổ sung ví dụ cụ thể nào không?

