# Hướng dẫn chi tiết: Commit theo chuẩn (Conventional Commits)

Tài liệu này giải thích chi tiết **Conventional Commits** — cách viết message commit rõ ràng, có cấu trúc, dễ tự động hoá (semantic versioning, changelog, CI).

---

## Mục đích

- Giữ lịch sử git nhất quán, dễ đọc.
- Hỗ trợ tự động hóa: release, semver, changelog.
- Giúp reviewer, đồng đội nhanh hiểu ý định thay đổi.

---

## Cấu trúc cơ bản

```
<type>(<scope>): <short summary>

<body>

<footer>
```

- **type**: loại thay đổi (bắt buộc).
- **scope** (tùy chọn): module/feature bị ảnh hưởng.
- **short summary**: mô tả ngắn, <= 50 ký tự, không kết thúc bằng dấu chấm.
- **body** (tùy chọn): giải thích chi tiết — lý do, cách làm, ảnh hưởng, ví dụ.
- **footer** (tùy chọn): metadata (ví dụ `BREAKING CHANGE: ...`, hoặc ticket IDs `Closes #123`).

---

## Các `type` phổ biến

| Type       | Ý nghĩa / Khi dùng                                |
| ---------- | ------------------------------------------------- |
| `feat`     | Thêm tính năng mới                                |
| `fix`      | Sửa lỗi                                           |
| `docs`     | Tài liệu (README, comment)                        |
| `style`    | Format, style, whitespace (không logic)           |
| `refactor` | Thay đổi code nhưng không thêm feature/bugfix     |
| `perf`     | Thay đổi cải thiện hiệu năng                      |
| `test`     | Thêm/sửa test                                     |
| `chore`    | Cập nhật công cụ/build/deps (không thay code app) |
| `ci`       | Cấu hình CI/CD                                    |
| `revert`   | Quay lại commit trước đó                          |

> Không viết hoa `Type`.

---

## Quy ước về nội dung

1. **Dòng đầu**: ngắn, súc tích, tối đa 50 ký tự.
2. **Dòng trống** sau dòng tiêu đề nếu có body.
3. **Body**: mô tả lý do thay đổi, không chỉ mô tả _cái gì_ mà nên nói _tại sao_ và _cách giải quyết_.

   - Giữ mỗi dòng <= 72 ký tự để hiển thị đẹp trong terminal.

4. **Footer**: dùng để ghi `BREAKING CHANGE` hoặc số issue, ticket.

   - `BREAKING CHANGE: mô tả thay đổi backward-incompatible` — bắt buộc kèm theo mô tả tác động và hướng dẫn upgrade.
   - Hoặc `Closes #123` / `Fixes #456` để tự đóng issue trên GitHub khi merge.

---

## Ví dụ cụ thể

**Thêm feature**

```
feat(auth): add JWT refresh token flow

- add refresh token endpoint
- store refresh tokens hashed in DB
- update docs with new token rotation strategy
```

**Sửa bug**

```
fix(movie-service): prevent duplicate movie insert

Previously insertion allowed duplicates when external id missing.
Add unique index and validation.
Closes #321
```

**Breaking change**

```
feat(api): replace /v1/user endpoint with /v2/users

BREAKING CHANGE: client must call /v2/users and include `X-Client-Version` header.
See migration.md for changes.
```

---

## Ánh xạ sang Semantic Versioning (tự động release)

- `feat` → **minor** (x.y+1.z)
- `fix` → **patch** (x.y.z+1)
- `BREAKING CHANGE` → **major** (x+1.y.z)

Công cụ như `semantic-release` hoặc `standard-version` dùng commit message để quyết version.

---

## Công cụ khuyến nghị để enforce

1. **Husky**: git hooks (pre-commit, commit-msg).
2. **commitlint**: kiểm tra message theo rule (conv. commits).
3. **Commitizen** (optional): giúp interactive tạo commit theo template.
4. **semantic-release**: tự sinh tag/version, changelog, release.

Ví dụ cài nhanh (npm):

```bash
npm install -D husky @commitlint/config-conventional @commitlint/cli commitizen
npx husky install
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
```

`.commitlintrc.json` ví dụ:

```json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "type-enum": [
      2,
      "always",
      [
        "feat",
        "fix",
        "docs",
        "style",
        "refactor",
        "perf",
        "test",
        "chore",
        "ci",
        "revert"
      ]
    ],
    "subject-case": [0]
  }
}
```

---

## Hook mẫu cho Husky (package.json scripts)

```json
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
}
```

Hoặc dùng `.husky/commit-msg` script như ở trên.

---

## Tips viết commit tốt

- Viết commit nhỏ, một mục tiêu rõ ràng.
- Nếu thay đổi lớn, chia thành nhiều commit theo chức năng.
- Commit message phải giải thích **tại sao** thay đổi, không chỉ **làm gì**.
- Dùng `scope` khi repo có nhiều module: `feat(user): ...`, `fix(api): ...`.
- Sử dụng `Closes #issue` để tự động đóng issue sau merge.

---

## Mẫu template để copy nhanh

```
<type>(<scope>): <short summary>

- <reason 1>
- <reason 2>

Closes #<issue>
BREAKING CHANGE: <details>
```

---

## Áp dụng cho project NestJS (ví dụ)

- `feat(api)`: thêm controller/endpoint mới.
- `fix(service)`: sửa bug trong service.
- `docs(readme)`: cập nhật README cài đặt.

Ví dụ commit cho thay đổi DB model:

```
refactor(database): normalize movie schema

- move actors into separate collection
- add indexes for search

BREAKING CHANGE: migration required. Run migrations/migrate-v2.js
```

---

## Gợi ý workflow

1. Bật `commitlint` + `husky` trên repo.
2. Hướng dẫn dev dùng `git commit` hoặc `npm run commit` (Commitizen).
3. Dùng `semantic-release` hoặc `standard-version` để auto release.
4. Viết CONTRIBUTING.md tóm tắt quy ước để dev mới dễ theo.

---

## Tài nguyên tham khảo ngắn

- Conventional Commits: [https://www.conventionalcommits.org](https://www.conventionalcommits.org)
- commitlint, husky, semantic-release (tìm trên npm)

---
