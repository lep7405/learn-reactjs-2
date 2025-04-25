# Các Component và Khái niệm Polaris Phổ biến Khác

## 1. Layout và Cấu trúc Trang

*   **`<Page>`**: Component gốc bao bọc toàn bộ nội dung của một trang trong ứng dụng Shopify. Cung cấp cấu trúc cơ bản và có thể bao gồm tiêu đề trang (`title`), hành động chính (`primaryAction`), hành động phụ (`secondaryActions`).
    ```jsx
    <Page
      title="Products"
      primaryAction={{ content: 'Add product', onAction: handleAdd }}
    >
      {/* Nội dung trang */}
    </Page>
    ```
*   **`<Layout>` và `<Layout.Section>`**: Dùng để tổ chức nội dung trang thành các cột và khu vực. `<Layout>` tạo ra một hệ thống lưới, và `<Layout.Section>` định nghĩa một khu vực bên trong lưới đó. Bạn có thể có nhiều section trong một layout.
    ```jsx
    <Layout>
      <Layout.Section>
        {/* Nội dung khu vực chính */}
      </Layout.Section>
      <Layout.Section variant="oneThird"> {/* Section phụ chiếm 1/3 */}
        {/* Nội dung phụ */}
      </Layout.Section>
    </Layout>
    ```
*   **`<Card>`**: Dùng để nhóm các nội dung liên quan lại với nhau trong một khung viền, thường có padding bên trong. Rất hữu ích để chứa `IndexTable`, form, hoặc các thông tin khác. Có thể có `title` và `sectioned` prop để tự động thêm `<Card.Section>`.
    ```jsx
    <Card title="Product Details" sectioned>
      <p>Some content inside the card section.</p>
    </Card>
    ```
*   **`<InlineStack>` / `<BlockStack>` (hoặc `<Stack>` / `<Stack.Item>`)**: Các component layout linh hoạt dựa trên Flexbox để sắp xếp các phần tử con theo chiều ngang (`InlineStack`) hoặc chiều dọc (`BlockStack`/`Stack vertical`). Cung cấp các props như `gap` (khoảng cách), `align` (căn chỉnh ngang), `blockAlign` (căn chỉnh dọc). Rất hữu ích thay thế cho các `div` với style inline phức tạp.
    ```jsx
    <InlineStack gap="400" align="center">
      <Button>Save</Button>
      <Button variant="primary">Publish</Button>
    </InlineStack>

    <BlockStack gap="200">
      <Text>Line 1</Text>
      <Text>Line 2</Text>
    </BlockStack>
    ```

## 2. Form và Input

*   **`<Form>` và `onSubmit`**: Component bao bọc các trường input, cung cấp ngữ cảnh cho form và xử lý sự kiện submit.
*   **`<FormLayout>` và `<FormLayout.Group>`**: Giúp sắp xếp các trường input (như `TextField`) một cách gọn gàng, thường là theo chiều dọc hoặc theo nhóm trên cùng một hàng.
*   **`<TextField>`**: Component input cơ bản cho văn bản, số, email, password, v.v. Có các props quan trọng như `label`, `value`, `onChange`, `error`, `placeholder`, `autoComplete`.
*   **`<Autocomplete>`**: Như bạn đã sử dụng, cho phép người dùng tìm kiếm và chọn từ một danh sách gợi ý. Kết hợp với `<Autocomplete.TextField>`.
*   **`<Button>`**: Component nút bấm đa năng. Các props quan trọng:
    *   `onClick`: Hàm xử lý khi nhấn nút.
    *   `children` hoặc `content`: Nội dung text của nút.
    *   `variant`: Kiểu nút ('primary', 'secondary', 'tertiary').
    *   `tone`: Màu sắc thể hiện mục đích ('critical' cho xóa, 'success' cho thành công).
    *   `icon`: Hiển thị icon bên trong nút.
    *   `loading`: Hiển thị trạng thái đang xử lý.
    *   `disabled`: Vô hiệu hóa nút.
    *   `size`: Kích thước nút ('slim', 'medium', 'large').
    *   `accessibilityLabel`: Mô tả cho trình đọc màn hình.

## 3. Phản hồi và Thông báo

*   **`<Modal>`**: Hiển thị nội dung trong một cửa sổ nổi lên trên trang chính. Dùng cho các hành động cần sự tập trung như thêm/sửa dữ liệu, xác nhận. Các props chính: `open`, `onClose`, `title`, `primaryAction`, `secondaryActions`, `children`.
*   **`<Banner>`**: Hiển thị một thông báo nổi bật ở đầu trang hoặc trong một khu vực cụ thể. Thường dùng để thông báo trạng thái (thành công, lỗi, cảnh báo, thông tin). Props chính: `title`, `status` ('success', 'critical', 'warning', 'info'), `onDismiss` (hàm để đóng banner).
*   **`<Toast>`**: Thông báo ngắn gọn, tạm thời xuất hiện ở cuối màn hình. Thường được quản lý thông qua `Frame` và `ToastProvider`.
*   **`<Spinner>`**: Hiển thị biểu tượng xoay tròn để báo hiệu trạng thái đang tải.

## 4. Hiển thị Nội dung

*   **`<Text>`**: Component linh hoạt để hiển thị văn bản với các kiểu dáng và ngữ nghĩa khác nhau. Props quan trọng:
    *   `variant`: Kích thước và kiểu chữ ('headingXs', 'headingSm', ..., 'bodyMd', 'bodySm', ...).
    *   `as`: Thẻ HTML gốc để render (ví dụ: 'p', 'span', 'h1', 'h2', ...).
    *   `fontWeight`: Độ đậm của chữ ('bold', 'semibold', 'medium', 'regular').
    *   `tone`: Màu sắc ngữ nghĩa ('critical', 'warning', 'success', 'subdued', 'disabled').
*   **`<Icon>`**: Hiển thị các icon từ thư viện `@shopify/polaris-icons`. Prop `source` nhận vào icon được import.

## 5. React Hooks (Không phải Polaris nhưng cần thiết)

*   **`useState`**: Quản lý state cục bộ của component (ví dụ: giá trị input, trạng thái mở/đóng modal).
*   **`useEffect`**: Thực thi các side effect (ví dụ: gọi API khi component mount, đồng bộ state với props).
*   **`useCallback`**: Memoize (ghi nhớ) các hàm callback để tránh tạo lại chúng một cách không cần thiết giữa các lần render, giúp tối ưu hóa khi truyền xuống component con được `React.memo`.
*   **`useMemo`**: Memoize các giá trị được tính toán phức tạp hoặc các đối tượng/mảng để tránh tính toán lại hoặc tạo lại chúng một cách không cần thiết giữa các lần render.

## 6. Accessibility (Khả năng tiếp cận)

Polaris chú trọng đến accessibility. Nhiều component có các props như `accessibilityLabel` để cung cấp mô tả rõ ràng cho người dùng sử dụng trình đọc màn hình, đặc biệt là với các nút chỉ có icon.

Đây là những thành phần cốt lõi bạn thường xuyên tương tác khi xây dựng giao diện với Shopify Polaris. Nắm vững chúng sẽ giúp bạn xây dựng ứng dụng hiệu quả và nhất quán.
