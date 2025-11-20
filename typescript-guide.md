
# HƯỚNG DẪN CHI TIẾT TYPESCRIPT

## 1. TypeScript là gì?
TypeScript là một ngôn ngữ lập trình được phát triển bởi Microsoft, là siêu tập (superset) của JavaScript, bổ sung hệ thống kiểu tĩnh (static typing) và các tính năng hướng đối tượng mạnh mẽ.

✅ **Khi nào dùng?**
- Dự án lớn, nhiều người cùng làm.
- Dự án React, Node.js, hoặc Angular hiện đại.
- Muốn giảm lỗi runtime, cải thiện maintainability.

## 2. Cài đặt TypeScript
```bash
npm install -g typescript
tsc --init
```
- `tsc`: Trình biên dịch TypeScript → JavaScript.
- `tsc --init`: Tạo file `tsconfig.json` để cấu hình dự án.

## 3. Kiểu dữ liệu cơ bản
```ts
let isDone: boolean = false;
let age: number = 30;
let name: string = "Alice";
let list: number[] = [1, 2, 3];
let tuple: [string, number] = ["hello", 10];
let notSure: any = 4;
```

✅ **Dùng khi**:
- Muốn rõ ràng kiểu biến → giúp dễ debug.

## 4. Enum
```ts
enum Color {
  Red,
  Green,
  Blue,
}
let c: Color = Color.Green;
```
✅ **Dùng khi**:
- Biểu diễn trạng thái hoặc lựa chọn cố định.

## 5. Hàm với kiểu
```ts
function add(x: number, y: number): number {
  return x + y;
}
```
✅ **Dùng khi**:
- Khai báo rõ kiểu input và output → gợi ý & kiểm tra.

## 6. Interface
```ts
interface Person {
  name: string;
  age: number;
}
function greet(person: Person) {
  console.log("Hello " + person.name);
}
```
✅ **Dùng khi**:
- Làm việc với object, props trong React.

## 7. Class và Kế thừa
```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m.`);
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof! Woof!");
  }
}
```
✅ **Dùng khi**:
- Viết ứng dụng hướng đối tượng.

## 8. Generic
```ts
function identity<T>(arg: T): T {
  return arg;
}
let output = identity<string>("myString");
```
✅ **Dùng khi**:
- Hàm/class làm việc với nhiều kiểu dữ liệu khác nhau.

## 9. Type Union & Intersection
```ts
function padLeft(value: string, padding: string | number) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  return padding + value;
}
```
✅ **Dùng khi**:
- Dữ liệu có thể ở nhiều dạng khác nhau.

## 10. Optional & Default Parameters
```ts
function buildName(firstName: string, lastName?: string): string {
  return lastName ? `${firstName} ${lastName}` : firstName;
}
```
✅ **Dùng khi**:
- Viết API hoặc hàm linh hoạt với tham số.

## 11. Type Assertion
```ts
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;
```
✅ **Dùng khi**:
- Làm việc với `unknown`, `any`, hoặc DOM API.

## 12. Kiểm tra với tsc
```bash
tsc file.ts
```
✅ **Dùng khi**:
- Biên dịch TS → JS hoặc kiểm tra lỗi tĩnh.

## 13. Sử dụng với React (JSX)
```tsx
interface Props {
  name: string;
}
const Hello: React.FC<Props> = ({ name }) => <div>Hello {name}</div>;
```
✅ **Dùng trong**:
- Dự án React sử dụng TypeScript.

---

## Tóm tắt nhanh

| Tính năng       | Khi nào dùng? |
|-----------------|----------------|
| Kiểu dữ liệu     | Mọi nơi cần sự rõ ràng |
| Enum            | Chọn lựa cố định |
| Interface       | Làm việc với object, props |
| Class           | Hướng đối tượng |
| Generic         | Tái sử dụng code với kiểu khác nhau |
| Union/Intersection | Dữ liệu có thể nhiều dạng |
| Assertion       | Khi chắc chắn kiểu dữ liệu |
| React + TS      | Props an toàn, gợi ý code mạnh mẽ |
