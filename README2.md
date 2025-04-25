# Sử dụng `IndexTable` trong Shopify Polaris

`IndexTable` là một component mạnh mẽ trong Polaris dùng để hiển thị danh sách các tài nguyên (resources) như sản phẩm, đơn hàng, khách hàng dưới dạng bảng, hỗ trợ sắp xếp, lọc, chọn và thực hiện các hành động hàng loạt.

## 1. Cấu trúc cơ bản

```jsx
import { Card, IndexTable, useIndexResourceState, Text } from '@shopify/polaris';

function MyResourceList({ resources }) { // resources là mảng dữ liệu, ví dụ: products
  const resourceName = {
    singular: 'resource', // Tên số ít
    plural: 'resources',  // Tên số nhiều
  };

  // Hook quản lý trạng thái chọn
  const {
    selectedResources,
    allResourcesSelected,
    handleSelectionChange,
  } = useIndexResourceState(resources);

  // Tạo các hàng của bảng
  const rowMarkup = resources.map(
    (resource, index) => (
      <IndexTable.Row
        id={resource.id} // ID duy nhất của tài nguyên, rất quan trọng!
        key={resource.id} // React key
        selected={selectedResources.includes(resource.id)} // Trạng thái chọn
        position={index} // Vị trí hàng (cho styling)
      >
        <IndexTable.Cell>
          {/* Nội dung ô, ví dụ: ID */}
          <Text variant="bodyMd" fontWeight="bold" as="span">
            {resource.id}
          </Text>
        </IndexTable.Cell>
        <IndexTable.Cell>{resource.name}</IndexTable.Cell>
        <IndexTable.Cell>{resource.details}</IndexTable.Cell>
        {/* Thêm các ô khác nếu cần */}
      </IndexTable.Row>
    ),
  );

  return (
    <Card>
      <IndexTable
        resourceName={resourceName} // Tên tài nguyên
        itemCount={resources.length} // Tổng số lượng tài nguyên
        selectedItemsCount={ // Số lượng đang được chọn
          allResourcesSelected ? 'All' : selectedResources.length
        }
        onSelectionChange={handleSelectionChange} // Hàm xử lý khi chọn/bỏ chọn
        headings={[ // Định nghĩa các cột tiêu đề
          { title: 'ID' },
          { title: 'Name' },
          { title: 'Details' },
          // Thêm tiêu đề khác nếu cần
        ]}
        // bulkActions={...} // Hành động hàng loạt (xem mục 4)
        // pagination={...} // Phân trang (nếu cần)
      >
        {rowMarkup} {/* Các hàng đã tạo */}
      </IndexTable>
    </Card>
  );
}
```

## 2. Quản lý Trạng thái Chọn (`useIndexResourceState`)

Đây là hook thiết yếu để quản lý việc người dùng chọn các hàng trong bảng.

*   **Input:** Mảng dữ liệu gốc (`resources`).
*   **Output:**
    *   `selectedResources`: Mảng chứa các `id` của những hàng đang được chọn.
    *   `allResourcesSelected`: Boolean, `true` nếu ô "chọn tất cả" ở header được check (hoặc tất cả các mục đang hiển thị được chọn).
    *   `handleSelectionChange`: Hàm callback. **Bắt buộc** phải truyền hàm này vào prop `onSelectionChange` của `<IndexTable>`. Nó sẽ được gọi bởi `IndexTable` khi trạng thái chọn thay đổi.

**Kết nối với `<IndexTable>`:**

*   `onSelectionChange={handleSelectionChange}`: Cho phép `IndexTable` cập nhật trạng thái chọn thông qua hook.
*   `selectedItemsCount`: Hiển thị số lượng mục đang chọn (sử dụng `selectedResources.length` và `allResourcesSelected`).
*   `selected={selectedResources.includes(resource.id)}`: Đặt trạng thái trực quan (ô checkbox) cho mỗi `<IndexTable.Row>`.

## 3. Hành động trên từng hàng (Row-Level Actions)

Thường là các nút Edit, Delete, View,... đặt trong một `<IndexTable.Cell>` cuối cùng.

```jsx
// Bên trong rowMarkup.map(...)
<IndexTable.Cell>
  <Button
    size="slim"
    onClick={(event) => {
      // QUAN TRỌNG: Ngăn sự kiện click lan lên Row và kích hoạt chọn hàng
      event.stopPropagation();
      // Gọi hàm xử lý edit
      editResource(resource.id);
    }}
    icon={<EditIcon />}
    accessibilityLabel={`Edit ${resource.name}`}
  />
  <Button
    size="slim"
    onClick={(event) => {
      event.stopPropagation();
      // Gọi hàm xử lý delete
      deleteResource(resource.id);
    }}
    icon={<DeleteIcon />}
    accessibilityLabel={`Delete ${resource.name}`}
    tone="critical"
  />
</IndexTable.Cell>
```

*   **`event.stopPropagation()`:** Rất quan trọng để ngăn việc click vào nút hành động cũng đồng thời kích hoạt việc chọn/bỏ chọn hàng đó.
*   Sử dụng các component Polaris khác như `Button`, `Icon` bên trong `Cell`.
*   Thêm `accessibilityLabel` để cải thiện khả năng tiếp cận.

## 4. Hành động hàng loạt (`bulkActions`)

Hiển thị một thanh công cụ phía trên bảng khi có ít nhất một hàng được chọn.

```jsx
// Bên ngoài .map, trong component cha
const bulkActions = [
  {
    content: 'Delete selected', // Nội dung nút
    onAction: () => {
      console.log('Deleting resources:', selectedResources);
      // Gọi hàm xử lý xóa hàng loạt với mảng selectedResources
      // Ví dụ: deleteMultipleResources(selectedResources);
    },
    // destructive: true, // Đánh dấu hành động nguy hiểm (thường là xóa)
  },
  {
    content: 'Edit tags',
    onAction: () => {
      console.log('Editing tags for:', selectedResources);
      // Mở modal hoặc thực hiện hành động sửa tag
    },
  },
  // Thêm các hành động khác nếu cần
];

// Truyền vào IndexTable
<IndexTable
  // ... các props khác
  bulkActions={bulkActions}
>
  {rowMarkup}
</IndexTable>
```

*   `bulkActions` là một mảng các object.
*   Mỗi object định nghĩa một hành động với `content` (text hiển thị) và `onAction` (hàm callback được gọi khi nhấn nút).
*   Hàm `onAction` có thể truy cập trực tiếp vào biến `selectedResources` từ `useIndexResourceState` trong scope của component cha.

## 5. Các Props Quan trọng khác

*   `headings`: Mảng các object `{title: 'Tên Cột'}` để định nghĩa tiêu đề cột. Thứ tự các tiêu đề phải khớp với thứ tự các `<IndexTable.Cell>` trong mỗi hàng.
*   `itemCount`: Tổng số lượng tài nguyên (quan trọng cho việc hiển thị thông tin và phân trang).
*   `resourceName`: Object `{ singular: '...', plural: '...' }` để hiển thị văn bản ngữ cảnh đúng (ví dụ: "Showing 5 products").
*   `loading`: (Boolean) Hiển thị trạng thái tải dữ liệu cho bảng.
*   `pagination`: Object chứa các props để điều khiển phân trang (ví dụ: `hasNext`, `hasPrevious`, `onNext`, `onPrevious`).

## 6. Tương tác với Cell Content

Nếu bạn muốn thực hiện hành động khi click vào nội dung *bên trong* một cell (không phải nút action) mà **không** muốn chọn hàng, hãy bọc nội dung đó trong một thẻ (ví dụ `<span>`) và sử dụng `onClick` với `event.stopPropagation()` trên thẻ đó.

```jsx
<IndexTable.Cell>
  <span onClick={(event) => {
    event.stopPropagation();
    console.log('Clicked on cell content:', resource.name);
    // Thực hiện hành động khác
  }}>
    {resource.name}
  </span>
</IndexTable.Cell>
```
