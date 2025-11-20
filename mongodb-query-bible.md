# MongoDB Query Bible

Tài liệu chi tiết các tham số và toán tử trong MongoDB (Query, Update,
Projection, Aggregation).

------------------------------------------------------------------------

## 1. Query Operators (Filter)

Dùng để lọc document trong `find`, `findOne`, `update`, `delete`...

### Toán tử so sánh

-   `$eq`: bằng
-   `$ne`: khác
-   `$gt`: lớn hơn
-   `$gte`: lớn hơn hoặc bằng
-   `$lt`: nhỏ hơn
-   `$lte`: nhỏ hơn hoặc bằng

**Ví dụ:**

``` js
db.users.find({ age: { $gte: 18, $lt: 30 } })
```

### Toán tử logic

-   `$and`: tất cả điều kiện đúng
-   `$or`: ít nhất 1 điều kiện đúng
-   `$nor`: phủ định OR
-   `$not`: phủ định điều kiện

**Ví dụ:**

``` js
db.users.find({
  $and: [
    { age: { $gte: 18 } },
    { $or: [{ city: "Hanoi" }, { city: "HCM" }] }
  ]
})
```

### Toán tử phần tử (Element Operators)

-   `$exists`: kiểm tra field tồn tại
-   `$type`: kiểm tra kiểu dữ liệu

``` js
db.users.find({ email: { $exists: true } })
db.logs.find({ value: { $type: "string" } })
```

### Toán tử mảng (Array Operators)

-   `$in`: nằm trong mảng
-   `$nin`: không nằm trong mảng
-   `$all`: chứa tất cả phần tử
-   `$size`: đúng kích thước mảng
-   `$elemMatch`: điều kiện cho phần tử trong mảng

``` js
db.users.find({ tags: { $all: ["tech", "ai"] } })
db.students.find({ scores: { $elemMatch: { $gte: 90, $lt: 100 } } })
```

### Toán tử đánh giá (Evaluation Operators)

-   `$regex`: so khớp biểu thức chính quy
-   `$expr`: so sánh giữa các field

``` js
db.users.find({ name: { $regex: "^Ph", $options: "i" } })
db.sales.find({ $expr: { $gt: ["$amount", "$target"] } })
```

------------------------------------------------------------------------

## 2. Projection (Field chọn trả về)

-   `1`: hiển thị
-   `0`: loại bỏ

``` js
db.users.find({}, { name: 1, email: 1, _id: 0 })
```

Có thể dùng toán tử: - `$slice`: cắt mảng
- `$meta`: lấy metadata (textScore, search)

``` js
db.articles.find(
  { $text: { $search: "mongodb" } },
  { score: { $meta: "textScore" } }
)
```

------------------------------------------------------------------------

## 3. Sort, Limit, Skip

-   `.sort({ field: 1 })`: tăng dần
-   `.sort({ field: -1 })`: giảm dần
-   `.limit(n)`: số document tối đa
-   `.skip(n)`: bỏ qua n document

``` js
db.users.find().sort({ age: -1 }).skip(20).limit(10)
```

------------------------------------------------------------------------

## 4. Collation (Ngôn ngữ, case-insensitive)

``` js
db.users.find({ name: "phở" }).collation({ locale: "vi", strength: 1 })
```

------------------------------------------------------------------------

## 5. Update Operators

### Field Operators

-   `$set`: cập nhật giá trị
-   `$unset`: xóa field
-   `$rename`: đổi tên field

``` js
db.users.updateOne({ name: "Pho" }, { $set: { age: 22 } })
```

### Arithmetic Operators

-   `$inc`: tăng/giảm
-   `$mul`: nhân

``` js
db.users.updateOne({ name: "Pho" }, { $inc: { score: 5 } })
```

### Array Update Operators

-   `$push`: thêm phần tử
-   `$addToSet`: thêm nếu chưa có
-   `$pull`: xóa phần tử
-   `$pop`: xóa đầu/cuối

``` js
db.users.updateOne(
  { name: "Pho" },
  { $push: { tags: { $each: ["newbie", "pro"], $position: 0 } } }
)
```

### Current Date Operators

-   `$currentDate`: gán ngày giờ hiện tại

``` js
db.users.updateOne({ name: "Pho" }, { $currentDate: { lastLogin: true } })
```

------------------------------------------------------------------------

## 6. Aggregation Pipeline

Dùng khi cần xử lý dữ liệu phức tạp.

### Các stage chính

-   `$match`: lọc dữ liệu
-   `$project`: chọn field
-   `$group`: gom nhóm
-   `$sort`: sắp xếp
-   `$limit`, `$skip`: phân trang
-   `$lookup`: join collection khác
-   `$unwind`: tách mảng thành nhiều document
-   `$addFields`: thêm field mới
-   `$count`: đếm document

**Ví dụ:**

``` js
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } }
])
```

### Toán tử trong `$group`

-   `$sum`: tính tổng
-   `$avg`: tính trung bình
-   `$min`, `$max`: nhỏ nhất/lớn nhất
-   `$first`, `$last`: lấy phần tử đầu/cuối

------------------------------------------------------------------------

## 7. Text Search

Cần tạo text index trước.

``` js
db.articles.createIndex({ content: "text" })
db.articles.find({ $text: { $search: "mongodb" } })
```

------------------------------------------------------------------------

## 8. Index & Performance Notes

-   Luôn tạo index cho field thường dùng trong filter/sort.
-   Dùng `.explain("executionStats")` để phân tích query.
-   Tránh dùng `$regex` bắt đầu bằng `^.*` (sẽ scan toàn bộ
    collection).
-   Hạn chế `skip` quá lớn (nên dùng range query thay thế).

------------------------------------------------------------------------

## Tóm tắt nhanh

-   **Filter Operators**: `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`,
    `$in`, `$nin`, `$regex`
-   **Logical Operators**: `$and`, `$or`, `$not`, `$nor`
-   **Element Operators**: `$exists`, `$type`
-   **Array Operators**: `$all`, `$elemMatch`, `$size`
-   **Update Operators**: `$set`, `$unset`, `$inc`, `$push`, `$pull`,
    `$addToSet`
-   **Aggregation Stages**: `$match`, `$group`, `$project`, `$sort`,
    `$lookup`, `$unwind`

------------------------------------------------------------------------
