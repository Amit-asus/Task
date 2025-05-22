

- ✅ Component creation
- 🔧 Type updates
- 🔄 UI integration
- ⚙️ Backend/API changes (if needed)

---

# 📦 Add Image Preview to Item Selector

This guide explains how to add an image preview feature to the `ItemSelectorControls` component by introducing a new `ImagePreview` component and updating data structures accordingly.

---

## ✅ 1. Create a New Component: `ImagePreview.tsx`

This component displays a clickable thumbnail of the selected item's image.

### 📁 File: `src/components/ImagePreview.tsx`

```tsx
import React from 'react';

interface ImagePreviewProps {
  imageUrl?: string;
  label: string;
}

const ImagePreview = ({ imageUrl, label }: ImagePreviewProps) => {
  if (!imageUrl) return null;

  return (
    <div className="mt-2">
      <a href={imageUrl} target="_blank" rel="noopener noreferrer">
        <img
          src={imageUrl}
          alt={label}
          className="w-24 h-24 object-contain border rounded"
        />
      </a>
    </div>
  );
};

export default ImagePreview;
```

---

## 🔧 2. Update `ItemOption` Type

Ensure the `ItemOption` type includes an optional `imageUrl` field.

### 📁 File: `src/features/procurement-requests/types/index.ts` or similar

```ts
export interface ItemOption {
  value: string;
  label: string;
  category: string;
  unit: string;
  tax: number;
  imageUrl?: string; // ✅ Added
}
```

---

## 🔄 3. Update Data Mapping (`itemOptions`)

When mapping your item list to `ItemOption`, include the `imageUrl`.

### Example Usage

```ts
const itemOptions = itemList.map(item => ({
  value: item.name,
  label: item.item_name,
  category: item.category,
  unit: item.unit_name,
  tax: item.tax,
  imageUrl: item.imageUrl || '/placeholder-image.png', // ✅ Include image URL
}));
```

---

## 🖼️ 4. Use `ImagePreview` in `ItemSelectorControls.tsx`

Insert the `ImagePreview` component below the item dropdown to show the selected item’s image.

### 📁 File: `src/features/procurement-requests/components/ItemSelectorControls.tsx`

Locate the item selector block and update it like this:

```tsx
{/* --- Item Selector --- */}
<div>
  <Label htmlFor="item-select" className="block text-sm font-medium text-gray-700 mb-1">
    Item <sup className="text-red-500">*</sup>
  </Label>

  <ReactSelect
    inputId='item-select'
    placeholder={"Select or Create/Request Item..."}
    value={curItem}
    isDisabled={disabled}
    options={itemOptions}
    onChange={handleItemChange}
    onAddItemClick={onOpenNewItemDialog}
    components={{ MenuList: CustomMenuList }}
    selectProps={{ customProps: itemSelectCustomProps }}
    isClearable
  />

  {/* --- Image Preview Goes Here --- */}
  <ImagePreview imageUrl={curItem?.imageUrl} label={curItem?.label || ''} />
</div>
```

---

## ⚙️ 5. Backend/API Changes (If Needed)

If the `imageUrl` is not already part of the API response for items, update the backend to include it. Ensure the data fetching logic retrieves this property.

### ✅ Step-by-step:

1. **Update Backend Schema / DTO**
   - Add `imageUrl` as a nullable string field in the `Item` model/response.
   - Example:
     ```json
     {
       "name": "Steel Rod",
       "item_name": "Reinforcement Bar",
       "category": "Construction",
       "unit_name": "kg",
       "tax": 18,
       "imageUrl": "https://example.com/images/steel-rod.jpg"
     }
     ```

2. **Update Database Query or ORM Mapping**
   - If images are stored separately, ensure the query joins or fetches the image URL correctly.
   - If using a file storage system (e.g., AWS S3), generate and return a signed URL or public path.

3. **Update API Endpoint(s)**
   - Modify any endpoints that return item data to include the `imageUrl` field.
   - Example endpoint: `GET /api/items`


5. **Frontend Integration**
   - Once the API returns `imageUrl`, frontend will automatically render the preview using the logic above.

---

## 📌 Summary


Now, whenever an item is selected, its associated image will be shown below the dropdown as a clickable preview!

