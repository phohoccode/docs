
# 📘 React `useTransition` - Cách hoạt động và các trường hợp sử dụng

`useTransition` là một hook trong React 18 dùng để đánh dấu các **cập nhật không khẩn cấp** (non-urgent updates). Nó giúp cải thiện hiệu năng và trải nghiệm người dùng khi thực hiện các thao tác tốn thời gian như filter, navigation, tính toán lớn,...

---

## 🚀 1. Cú pháp

```tsx
const [isPending, startTransition] = useTransition();

startTransition(() => {
  // cập nhật không gấp ở đây
});
```

- `startTransition`: Hàm dùng để wrap các cập nhật "không khẩn cấp"
- `isPending`: Biến boolean, `true` khi cập nhật đang diễn ra, `false` khi đã hoàn tất

---

## 💡 2. Vì sao cần `useTransition`?

- Giúp React **ưu tiên các cập nhật UI khẩn cấp** (như nhập liệu, click)
- Các cập nhật chậm như fetch dữ liệu, cập nhật router, filter list lớn sẽ không làm UI bị đơ

---

## ✅ 3. Các trường hợp sử dụng phổ biến

### 3.1. 📥 Cập nhật danh sách lọc lớn (filter list)

```tsx
const [list, setList] = useState([]);
const [isPending, startTransition] = useTransition();

const handleFilter = (query) => {
  startTransition(() => {
    const filtered = heavyFilter(query);
    setList(filtered);
  });
};
```

👉 `isPending === true` khi danh sách đang được lọc lại

---

### 3.2. 🔁 Thay đổi router với `next/router` hoặc `next/navigation`

```tsx
const [isPending, startTransition] = useTransition();
const router = useRouter();

const handleClick = () => {
  startTransition(() => {
    router.replace("?page=2", { scroll: false });
  });
};
```

👉 `isPending === true` cho đến khi trang render xong → dùng để show spinner, disable button, v.v.

---

### 3.3. 🧠 Cập nhật state lớn hoặc nhiều lần setState

```tsx
startTransition(() => {
  setItems(heavyCalculation());
  setVisible(true);
});
```

👉 Nếu không dùng transition, React có thể chậm khi re-render toàn bộ component

---

### 3.4. 📝 Gõ input → cập nhật gợi ý tìm kiếm chậm

```tsx
const [input, setInput] = useState("");
const [suggestions, setSuggestions] = useState([]);
const [isPending, startTransition] = useTransition();

const handleChange = (e) => {
  const value = e.target.value;
  setInput(value);

  startTransition(() => {
    const result = getSuggestions(value); // xử lý nặng
    setSuggestions(result);
  });
};
```

👉 `input` được cập nhật ngay, nhưng `suggestions` sẽ chậm lại để không gây giật

---

### 3.5. ⏳ Show loading spinner khi navigation

```tsx
{isPending ? <Spinner /> : <MovieList data={data} />}
```

---

## 🔍 4. Lưu ý quan trọng

| Điều cần nhớ | Ghi chú |
|--------------|--------|
| Không thay thế `useEffect` | `useTransition` chỉ để **đánh dấu** cập nhật không gấp |
| Không tự động hoãn render | Nó chỉ giúp React **ưu tiên cập nhật UI trước** |
| Không liên quan đến `useDeferredValue` | `useTransition` là **trigger**, còn `useDeferredValue` là **reactive value** |

---

## ✅ 5. Khi nào nên dùng?

| Tình huống | Dùng `useTransition`? |
|-----------|------------------------|
| Gõ input → lọc danh sách lớn | ✅ Có |
| Cập nhật router trong Next.js | ✅ Có |
| Bấm nút → gọi API rồi setState | ⚠️ Có thể (nếu UI bị giật) |
| Chỉ cập nhật nhỏ, nhanh | ❌ Không cần |
| Gọi setState ngay sau click | ❌ Không cần |

---

## 📚 6. Thêm: So sánh `useTransition` vs `useDeferredValue`

| `useTransition` | `useDeferredValue` |
|------------------|----------------------|
| Trigger hành động | Chỉ trì hoãn giá trị |
| Dùng để wrap logic | Dùng để bọc biến (state) |
| Theo dõi `isPending` | Theo dõi `deferredValue` |

---

## 📌 7. Tổng kết

- `useTransition` rất hữu ích khi làm việc với thao tác lớn → tránh UI bị đơ
- Bạn nên sử dụng nó khi cần feedback loading và không cần render ngay lập tức
- Kết hợp tốt với `router.push/replace`, filter list, nhập liệu,...

---

## 🧪 8. Demo code tối giản

```tsx
const [isPending, startTransition] = useTransition();

const handleClick = () => {
  startTransition(() => {
    doHeavyWork();
    setShow(true);
  });
};

return (
  <>
    <button onClick={handleClick} disabled={isPending}>
      {isPending ? "Đang xử lý..." : "Bắt đầu"}
    </button>
  </>
);
```

---

*Viết bởi ChatGPT – Cập nhật tháng 7/2025*
