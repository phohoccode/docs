# Hướng dẫn chi tiết các phương thức của Object, Array, Set và Map trong TypeScript ✨📘🧩

> **Lưu ý**: TypeScript là superset của JavaScript nên các phương thức dưới đây cũng giống JavaScript. Chỉ khác ở chỗ TypeScript hỗ trợ kiểu dữ liệu tĩnh giúp code an toàn và dễ bảo trì hơn. 💡📌📝

---

## 1. Object 🧱✨🔧

Các phương thức sau thường được dùng để tương tác với các thuộc tính của object.

### 1.1 `Object.keys(obj)`

- **Chức năng**: Trả về một **mảng** chứa các **tên thuộc tính có thể liệt kê (enumerable)** dạng **string** của object.
- **Ví dụ**:
  ```ts
  const obj = { a: 1, b: 2, 10: "c" };
  // Kết quả: ['10', 'a', 'b'] (key số sẽ được sắp xếp trước)
  console.log(Object.keys(obj));
  ```
- **Sử dụng**: Thích hợp khi cần **duyệt qua** các key của object hoặc kiểm tra sự **tồn tại** của key.

### 1.2 `Object.values(obj)`

- **Chức năng**: Trả về một **mảng** chứa các **giá trị** của các thuộc tính có thể liệt kê của object.
- **Ví dụ**:
  ```ts
  const obj = { a: 1, b: 2 };
  console.log(Object.values(obj)); // Kết quả: [1, 2]
  ```
- **Sử dụng**: Hữu ích khi muốn **xử lý dữ liệu** (ví dụ: tính tổng, tìm max/min) mà **không quan tâm** đến tên key.

### 1.3 `Object.entries(obj)`

- **Chức năng**: Trả về một **mảng** của các cặp `[key, value]` của các thuộc tính có thể liệt kê của object.
- **Ví dụ**:
  ```ts
  const obj = { a: 1, b: 2 };
  console.log(Object.entries(obj)); // Kết quả: [['a', 1], ['b', 2]]
  ```
- **Sử dụng**: Dùng khi cần **duyệt object** và truy cập cả key lẫn value, hoặc khi muốn **chuyển đổi object thành dạng mảng** để dùng các phương thức của Array (như `map`, `filter`).

### 1.4 `Object.fromEntries(entries)`

- **Chức năng**: Thực hiện thao tác **ngược lại** của `Object.entries()`, chuyển một mảng các cặp `[key, value]` thành một object.
- **Ví dụ**:
  ```ts
  const entries = [
    ["a", 1],
    ["b", 2],
  ];
  console.log(Object.fromEntries(entries)); // Kết quả: { a: 1, b: 2 }
  ```
- **Sử dụng**: Thường được dùng sau khi bạn đã dùng `Object.entries()`, sau đó **map/filter** mảng các cặp key–value, và cuối cùng là **chuyển ngược** kết quả về object.

### 1.5 `Object.assign(target, source)`

- **Chức năng**: Sao chép tất cả các thuộc tính có thể liệt kê từ một hoặc nhiều object nguồn (`source`) sang object đích (`target`). Nó trả về object đích đã được thay đổi.
- **Ví dụ**:
  ```ts
  const a = { x: 1 };
  const b = { y: 2 };
  const merged = Object.assign({}, a, b);
  console.log(merged); // Kết quả: { x: 1, y: 2 }
  // Lưu ý: Dùng {} làm target để tạo một object mới (clone/merge), tránh thay đổi a hoặc b.
  ```
- **Sử dụng**: Phù hợp khi **clone** (sao chép nông) hay **merge** (gộp) các thuộc tính của nhiều object (ví dụ: gộp cấu hình).

### 1.6 `Object.hasOwn(obj, key)`

- **Chức năng**: Kiểm tra xem object có sở hữu trực tiếp thuộc tính đã cho (`key`) hay không.
- **Ví dụ**:
  ```ts
  console.log(Object.hasOwn({ a: 1 }, "a")); // Kết quả: true
  // Khác với 'a' in obj, phương thức này không kiểm tra prototype chain.
  ```
- **Sử dụng**: Đáng tin cậy hơn khi kiểm tra sự tồn tại của key mà **không muốn bị ảnh hưởng** bởi các thuộc tính kế thừa từ **prototype chain**.

### 1.7 `Object.freeze(obj)`

- **Chức năng**: **Đóng băng** object, ngăn không cho thuộc tính mới được thêm vào, thuộc tính hiện có bị xóa, hoặc giá trị/thuộc tính ghi của các thuộc tính hiện có bị thay đổi.
- **Ví dụ**:
  ```ts
  const obj = Object.freeze({ a: 1 });
  obj.a = 5; // Không có tác dụng trong chế độ nghiêm ngặt (strict mode)
  delete obj.a; // Không có tác dụng
  console.log(obj); // Kết quả: { a: 1 }
  ```
- **Sử dụng**: Phù hợp cho việc tạo ra **dữ liệu bất biến (immutable)**.

### 1.8 `Object.seal(obj)`

- **Chức năng**: **Niêm phong** object, ngăn không cho thuộc tính mới được thêm vào hoặc thuộc tính hiện có bị xóa, nhưng **cho phép** thay đổi giá trị của các thuộc tính hiện có.
- **Ví dụ**:
  ```ts
  const obj = Object.seal({ a: 1 });
  obj.a = 3; // Được phép thay đổi giá trị
  obj.b = 4; // Bị ngăn chặn thêm key mới
  delete obj.a; // Bị ngăn chặn xóa key
  console.log(obj); // Kết quả: { a: 3 }
  ```
- **Sử dụng**: Dùng cho các object cần có **cấu trúc cố định** nhưng **giá trị** có thể thay đổi.

---

## 2. Array 📚✨🌀

Các phương thức của Array được chia thành hai nhóm chính: duyệt/biến đổi dữ liệu và thao tác trực tiếp với mảng.

### 2.1 Duyệt, Tìm kiếm và Biến đổi mảng (Thường không thay đổi mảng gốc - Immutable)

| Phương thức                     | Chức năng chi tiết                                                                                                                                              | Ví dụ                                               |
| :------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------- |
| **`forEach(callback)`**         | Thực thi hàm callback trên từng phần tử. **Không trả về** mảng mới. Phù hợp khi chỉ cần thực hiện tác vụ phụ (side effect) như `console.log` hoặc gọi API.      | `[1, 2].forEach(x => console.log(x))`               |
| **`map(callback)`**             | Biến đổi từng phần tử và **trả về mảng mới** với cùng số lượng phần tử. Dùng nhiều nhất trong xử lý dữ liệu để chuyển đổi định dạng.                            | `[1, 2, 3].map(x => x * 2)` // `[2, 4, 6]`          |
| **`filter(callback)`**          | Lọc phần tử theo điều kiện. **Trả về mảng mới** chỉ chứa các phần tử thỏa mãn `callback` trả về `true`.                                                         | `[1, 2, 3, 4].filter(x => x % 2 === 0)` // `[2, 4]` |
| **`reduce(callback, initial)`** | **Tính toán giá trị tích lũy** (accumulator) từ trái sang phải, áp dụng một hàm lên từng phần tử. Dùng để tính tổng, tính trung bình, hoặc **group** dữ liệu.   | `[1, 2, 3].reduce((sum, x) => sum + x, 0)` // `6`   |
| **`some(callback)`**            | Kiểm tra xem **ít nhất một** phần tử có thỏa mãn điều kiện không. **Trả về boolean** (`true`/`false`). Ngừng duyệt ngay khi tìm thấy phần tử đầu tiên thỏa mãn. | `[1, 3, 5].some(x => x > 4)` // `true`              |
| **`every(callback)`**           | Kiểm tra xem **tất cả** các phần tử có thỏa mãn điều kiện không. **Trả về boolean**. Ngừng duyệt ngay khi tìm thấy phần tử đầu tiên **không** thỏa mãn.         | `[2, 4, 6].every(x => x % 2 === 0)` // `true`       |
| **`find(callback)`**            | **Tìm và trả về phần tử** đầu tiên thỏa mãn điều kiện. Trả về `undefined` nếu không tìm thấy.                                                                   | `[1, 2, 3].find(x => x === 2)` // `2`               |
| **`findIndex(callback)`**       | **Tìm và trả về index** của phần tử đầu tiên thỏa mãn điều kiện. Trả về `-1` nếu không tìm thấy.                                                                | `[10, 20, 30].findIndex(x => x === 20)` // `1`      |

---

### 2.2 Thao tác mảng (Một số thay đổi mảng gốc - Mutable, một số thì không)

| Phương thức                                | Chức năng chi tiết                                                                                                             | Thay đổi mảng gốc? | Ví dụ                                                                 |
| :----------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------- | :----------------- | :-------------------------------------------------------------------- |
| **`push(item)`**                           | Thêm một hoặc nhiều phần tử vào **cuối** mảng. Trả về **độ dài mới** của mảng.                                                 | **Có**             | `const arr = [1]; arr.push(2);` // `[1, 2]`                           |
| **`pop()`**                                | **Xóa** phần tử cuối cùng và **trả về** phần tử đó.                                                                            | **Có**             | `const arr = [1, 2]; arr.pop();` // `[1]`, trả về `2`                 |
| **`shift()`**                              | **Xóa** phần tử **đầu tiên** và **trả về** phần tử đó.                                                                         | **Có**             | `const arr = [1, 2]; arr.shift();` // `[2]`, trả về `1`               |
| **`unshift(item)`**                        | Thêm một hoặc nhiều phần tử vào **đầu** mảng. Trả về **độ dài mới** của mảng.                                                  | **Có**             | `const arr = [2]; arr.unshift(1);` // `[1, 2]`                        |
| **`concat(arr)`**                          | Gộp nhiều mảng thành **một mảng mới**. Không thay đổi các mảng gốc.                                                            | Không              | `[1, 2].concat([3, 4])` // `[1, 2, 3, 4]`                             |
| **`slice(start, end)`**                    | **Cắt** một phần của mảng và trả về **mảng mới**. **Không** thay đổi mảng gốc.                                                 | Không              | `[1, 2, 3, 4].slice(1, 3)` // `[2, 3]`                                |
| **`splice(start, deleteCount, ...items)`** | **Xóa, chèn, hoặc thay thế** các phần tử tại một vị trí cụ thể. **Thay đổi mảng gốc**. Trả về mảng chứa các phần tử đã bị xóa. | **Có**             | `const arr = [1, 2, 3]; arr.splice(1, 1, 9);` // `arr` là `[1, 9, 3]` |
| **`includes(value)`**                      | Kiểm tra xem mảng có chứa một giá trị cụ thể hay không. Trả về boolean.                                                        | Không              | `[1, 2, 3].includes(2)` // `true`                                     |
| **`indexOf(value)`**                       | Trả về **vị trí (index) đầu tiên** của giá trị tìm thấy. Trả về `-1` nếu không tìm thấy.                                       | Không              | `['a', 'b', 'c'].indexOf('b')` // `1`                                 |
| **`join(separator)`**                      | Ghép tất cả phần tử trong mảng thành một **chuỗi**, phân tách bởi `separator`.                                                 | Không              | `[1, 2, 3].join('-')` // `"1-2-3"`                                    |
| **`flat(depth)`**                          | **Làm phẳng** mảng lồng nhau đến độ sâu (`depth`) xác định. Trả về **mảng mới**.                                               | Không              | `[1, [2, [3]]].flat(2)` // `[1, 2, 3]`                                |

---

## 3. Set 🔒✨🌱

**Set** là một cấu trúc dữ liệu dùng để lưu trữ các **giá trị duy nhất** (unique values) của bất kỳ kiểu nào. Nó thích hợp cho danh sách không muốn có sự trùng lặp.

### 3.1 Khởi tạo

- **Chức năng**: Tạo một đối tượng Set mới. Có thể truyền vào một Iterable (như Array) để khởi tạo.
- **Ví dụ**:
  ```ts
  const s = new Set([1, 2, 2, 3]);
  console.log(s); // Kết quả: Set(3) { 1, 2, 3 }
  ```

### 3.2 `add(value)`

- **Chức năng**: Thêm một giá trị vào Set. Nếu giá trị đã tồn tại, nó sẽ **tự động bỏ qua** (Set vẫn không thay đổi kích thước).
- **Ví dụ**:
  ```ts
  const s = new Set();
  s.add(1);
  s.add(1); // Bỏ qua
  console.log(s.size); // Kết quả: 1
  ```

### 3.3 `delete(value)`

- **Chức năng**: Xóa một phần tử khỏi Set. Trả về `true` nếu phần tử tồn tại và đã được xóa, ngược lại là `false`.
- **Ví dụ**:
  ```ts
  const s = new Set([1, 2]);
  s.delete(1);
  console.log(s); // Kết quả: Set(1) { 2 }
  ```

### 3.4 `has(value)`

- **Chức năng**: Kiểm tra xem Set có chứa một giá trị cụ thể hay không. Trả về boolean.
- **Ví dụ**:
  ```ts
  console.log(new Set([1, 2]).has(2)); // Kết quả: true
  ```

### 3.5 `clear()`

- **Chức năng**: Xóa toàn bộ phần tử khỏi Set.
- **Ví dụ**:
  ```ts
  const s = new Set([1, 2]);
  s.clear();
  console.log(s.size); // Kết quả: 0
  ```

### 3.6 `size`

- **Chức năng**: Một thuộc tính (property), trả về **số lượng** phần tử (giá trị duy nhất) hiện có trong Set.
- **Ví dụ**:
  ```ts
  console.log(new Set([1, 2, 3]).size); // Kết quả: 3
  ```

### 3.7 Duyệt Set

- **Chức năng**: Set là một đối tượng Iterable, có thể duyệt qua các giá trị của nó bằng vòng lặp `for...of`.
- **Ví dụ**:
  ```ts
  for (const value of new Set([1, 2])) console.log(value);
  // In ra: 1, 2
  ```

---

## 4. Map 🗺️🔑📦

**Map** là một cấu trúc dữ liệu lưu trữ các cặp **key–value**. Khác với Object, Map cho phép dùng **mọi kiểu dữ liệu** (object, hàm, hoặc giá trị nguyên thủy) làm **key**, linh hoạt và an toàn hơn khi làm việc với các key không phải string.

### 4.1 Khởi tạo

- **Chức năng**: Tạo một đối tượng Map mới. Có thể khởi tạo bằng một mảng các cặp `[key, value]`.
- **Ví dụ**:
  ```ts
  const m = new Map<string, number>(); // TypeScript giúp định kiểu rõ ràng
  m.set("a", 1);
  console.log(m.size); // Kết quả: 1
  ```

### 4.2 `set(key, value)`

- **Chức năng**: Thêm một cặp key–value mới vào Map, hoặc **cập nhật** giá trị nếu key đã tồn tại.
- **Ví dụ**:
  ```ts
  const m = new Map();
  m.set("x", 10);
  m.set("x", 20); // Cập nhật
  console.log(m.get("x")); // Kết quả: 20
  ```

### 4.3 `get(key)`

- **Chức năng**: Trả về **giá trị** liên kết với key. Trả về `undefined` nếu key không tồn tại.
- **Ví dụ**:
  ```ts
  const m = new Map([["a", 1]]);
  m.get("a"); // Kết quả: 1
  m.get("b"); // Kết quả: undefined
  ```

### 4.4 `has(key)`

- **Chức năng**: Kiểm tra xem Map có chứa key cụ thể hay không. Trả về boolean.
- **Ví dụ**:
  ```ts
  console.log(new Map([["a", 1]]).has("a")); // Kết quả: true
  ```

### 4.5 `delete(key)`

- **Chức năng**: Xóa cặp key–value khỏi Map. Trả về `true` nếu phần tử đã tồn tại và đã được xóa, ngược lại là `false`.
- **Ví dụ**:
  ```ts
  const m = new Map([["a", 1]]);
  m.delete("a");
  console.log(m.size); // Kết quả: 0
  ```

### 4.6 `clear()`

- **Chức năng**: Xóa toàn bộ cặp key–value khỏi Map.
- **Ví dụ**:
  ```ts
  const m = new Map([["a", 1]]);
  m.clear();
  console.log(m.size); // Kết quả: 0
  ```

### 4.7 `size`

- **Chức năng**: Một thuộc tính (property), trả về **số lượng** cặp key–value hiện có trong Map.
- **Ví dụ**:
  ```ts
  console.log(
    new Map([
      ["a", 1],
      ["b", 2],
    ]).size
  ); // Kết quả: 2
  ```

### 4.8 Duyệt Map

- **Chức năng**: Map là một đối tượng Iterable, có thể duyệt qua các cặp `[key, value]` của nó bằng vòng lặp `for...of`.
- **Ví dụ**:
  ```ts
  const m = new Map([
    ["a", 1],
    ["b", 2],
  ]);
  for (const [key, value] of m) {
    console.log(`${key}: ${value}`);
  }
  // In ra: a: 1, b: 2
  ```
