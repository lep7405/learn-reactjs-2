# Tối ưu hóa Render trong React với `useCallback` và `React.memo`

## Vấn đề: Render lại không cần thiết

Trong React, khi một component cha render lại (do state hoặc props của nó thay đổi), tất cả các component con của nó cũng sẽ mặc định render lại. Điều này có thể dẫn đến các vấn đề về hiệu năng, đặc biệt là khi component con phức tạp hoặc khi component cha render lại thường xuyên.

Vấn đề trở nên rõ ràng hơn khi truyền các hàm (callbacks) làm props từ cha xuống con. Ngay cả khi logic của hàm không đổi, một **phiên bản hàm mới** sẽ được tạo ra mỗi khi component cha render.

```jsx
// Component Cha
function Parent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // Hàm này được TẠO MỚI mỗi khi Parent render (ví dụ khi otherState thay đổi)
  const handleClick = () => {
    console.log("Button clicked!");
  };

  return (
    <div>
      <button onClick={() => setOtherState(s => !s)}>Toggle Other State</button>
      {/* Child nhận một prop handleClick MỚI mỗi khi Parent render */}
      <Child onClick={handleClick} />
    </div>
  );
}

// Component Con
function Child({ onClick }) {
  console.log("Child rendered");
  return <button onClick={onClick}>Click Me</button>;
}
```

Trong ví dụ trên, mỗi khi bạn nhấn nút "Toggle Other State", `Parent` sẽ render lại, tạo ra hàm `handleClick` mới, và `Child` cũng sẽ render lại, mặc dù hàm `handleClick` về mặt logic không thay đổi và `Child` không phụ thuộc vào `otherState`.

## Giải pháp 1: `React.memo`

`React.memo` là một Higher-Order Component (HOC). Nó bao bọc component của bạn và thực hiện việc "ghi nhớ" (memoization). `React.memo` sẽ so sánh nông (shallow comparison) các props của component giữa lần render trước và lần render hiện tại. Nếu các props không thay đổi, React sẽ bỏ qua việc render lại component đó và sử dụng kết quả render trước đó.

```jsx
const MemoizedChild = React.memo(function Child({ onClick }) {
  console.log("MemoizedChild rendered");
  return <button onClick={onClick}>Click Me</button>;
});

function Parent() {
  // ... state ...
  const handleClick = () => { /* ... */ };

  return (
    <div>
      {/* ... */}
      {/* Truyền handleClick xuống MemoizedChild */}
      <MemoizedChild onClick={handleClick} />
    </div>
  );
}
```

Tuy nhiên, `React.memo` **vẫn chưa đủ** để giải quyết vấn đề với các hàm callback được tạo mới mỗi lần render. Vì `handleClick` là một hàm mới mỗi khi `Parent` render, phép so sánh nông của `React.memo` sẽ thấy prop `onClick` đã thay đổi, và `MemoizedChild` vẫn sẽ render lại không cần thiết.

## Giải pháp 2: `useCallback`

Hook `useCallback` được sử dụng để memoize các hàm callback. Nó trả về một phiên bản được ghi nhớ của hàm đó, và phiên bản này chỉ thay đổi nếu một trong các dependencies (phần tử trong mảng phụ thuộc) của nó thay đổi.

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(false);

  // Hàm handleClick chỉ được tạo lại KHI count thay đổi
  const handleClick = useCallback(() => {
    console.log("Button clicked! Count is:", count);
  }, [count]); // Dependency là count

  // Hàm này cũng được memoize, không có dependency nên chỉ tạo 1 lần
  const toggleOtherState = useCallback(() => {
     setOtherState(s => !s)
  }, []);

  console.log("Parent rendered");

  return (
    <div>
      <button onClick={toggleOtherState}>Toggle Other State</button>
      {/* MemoizedChild nhận CÙNG MỘT hàm handleClick trừ khi count thay đổi */}
      <MemoizedChild onClick={handleClick} />
    </div>
  );
}

const MemoizedChild = React.memo(function Child({ onClick }) {
  console.log("MemoizedChild rendered");
  return <button onClick={onClick}>Click Me</button>;
});
```

## Kết hợp `useCallback` và `React.memo`

Sự kết hợp của `useCallback` trong component cha (để ổn định các hàm callback) và `React.memo` trong component con (để so sánh props) là chìa khóa để tối ưu hóa:

1.  **`useCallback` (trong Cha):** Đảm bảo rằng các hàm được truyền xuống dưới dạng props không phải là các phiên bản mới mỗi lần cha render (trừ khi dependencies của chúng thay đổi).
2.  **`React.memo` (trong Con):** So sánh các props. Vì các hàm callback (và các props khác không thay đổi) giờ đây giữ nguyên tham chiếu giữa các lần render của cha, `React.memo` sẽ thấy rằng các props không thay đổi và sẽ bỏ qua việc render lại component con.

**Khi nào nên sử dụng?**

*   Khi bạn truyền callbacks xuống các component con được tối ưu hóa bằng `React.memo`.
*   Khi component con thực hiện render tốn kém và bạn muốn tránh việc render lại không cần thiết.
*   Khi component cha render lại thường xuyên.

**Lưu ý:** Đừng lạm dụng `useCallback`. Việc sử dụng nó cho mọi hàm có thể làm tăng độ phức tạp không cần thiết. Chỉ sử dụng khi bạn thực sự cần tối ưu hóa việc render lại của component con hoặc khi hàm callback là dependency của một hook khác (như `useEffect`).
