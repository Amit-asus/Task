
---

## 🧾 **Component Overview**

### ✅ **Component Name**
`EditItemDialog`

### 📄 **File Path**
`src/features/procurement-requests/components/EditItemDialog.tsx`

### 🎯 **Purpose**
This is a **dialog/modal** used to:
- Edit an existing procurement request item.
- Allow users to update:
  - Quantity
  - Item name (if it’s a request item)
  - Category (if it’s a request item)
  - Make (with support for managing associated makes)
  - Unit
  - Comment
- Provide options to:
  - Delete the item
  - Save updates

It supports both **request items** and **non-request items**, with more editable fields available in the former case.

---

## 🔌 **Inputs / Props**

### ⚙️ **Props Interface: `EditItemDialogProps`**

```ts
interface EditItemDialogProps {
  isOpen: boolean;
  onOpenChange: (isOpen: boolean) => void;
  itemToEdit: ProcurementRequestItem | null;
  categories: Category[];
  onSubmitUpdate: (updatedItem: ProcurementRequestItem) => void;
  onDeleteItem: (itemName: string) => void;

  // Make-related props
  allMakeOptions: MakeOption[];
  initialCategoryMakes: CategoryMakesMap;
  selectedCategories: CategorySelection[];
  updateCategoryMakesInStore: (categoryName: string, newMake: string) => void;
  makeList?: Makelist[];
  makeListMutate: () => Promise<any>;
  categoryMakelist?: Makelist[];
  categoryMakeListMutate?: () => Promise<any>;
}
```

| Prop | Type | Description |
|------|------|-------------|
| `isOpen` | `boolean` | Controls whether the dialog is visible. |
| `onOpenChange` | `(isOpen: boolean) => void` | Callback when dialog open state changes. |
| `itemToEdit` | `ProcurementRequestItem \| null` | The item being edited; if null, dialog shows nothing. |
| `categories` | `Category[]` | List of available categories for selection. |
| `onSubmitUpdate` | `(updatedItem: ProcurementRequestItem) => void` | Called when user clicks "Update Item". |
| `onDeleteItem` | `(itemName: string) => void` | Called when user deletes the item. |
| `allMakeOptions` | `MakeOption[]` | List of all available makes. |
| `initialCategoryMakes` | `CategoryMakesMap` | Mapping from category names to their associated make values. |
| `selectedCategories` | `CategorySelection[]` | Current selected categories with their make lists. |
| `updateCategoryMakesInStore` | `(cat: string, make: string) => void` | Updates make list in store for a given category. |
| `makeList` | `Makelist[]` | Optional full list of makes (used by sub-dialog). |
| `makeListMutate` | `() => Promise<any>` | Function to refresh make data after changes. |
| `categoryMakelist`, `categoryMakeListMutate` | Optional | May be used for additional category-make operations. |

---

## 🔄 **Outputs / Events**

The component triggers two main events:

1. **`onSubmitUpdate(updatedItem)`**
   - Fired when user clicks **"Update Item"**.
   - Returns the updated item object with changes made in the dialog.

2. **`onDeleteItem(itemName)`**
   - Fired when user clicks **"Delete Item"**.
   - Returns the name or ID of the deleted item.

Additionally, internal state changes can also trigger UI updates like toast messages via `useToast`.

---

## 🧠 **Internal State Management**

### 📦 Local State

Defined using `useState`:

```ts
const [editState, setEditState] = useState<EditState | null>(null);
const [isManageMakesDialogOpen, setIsManageMakesDialogOpen] = useState(false);
```

#### `EditState` Interface

```ts
interface EditState {
    quantity: string;
    comment: string;
    category?: string;
    itemName?: string;
    unitName?: string;
    makeValue?: string;
}
```

- Holds the temporary values entered during editing.
- Resets when dialog closes (`!isOpen`).

#### `isManageMakesDialogOpen`
- Controls visibility of the nested `ManageCategoryMakesDialog`.
- Opens when the user clicks “Manage Makes” inside the Make selector.

---

### 💡 Derived State (via `useMemo`)

1. **`catOptions`**
   - Transforms raw `categories` into select-friendly options.
   - Used in the Category dropdown.

2. **`availableMakeOptions`**
   - Filters `allMakeOptions` based on currently selected category.
   - Ensures only relevant makes are shown.

3. **`currentMakeOption`**
   - Finds the currently selected make option object from `editState.makeValue`.

---

### 🔄 Effects (via `useEffect`)

```ts
useEffect(() => {
  if (itemToEdit && isOpen) {
    // Initialize editState with current item values
  } else if (!isOpen) {
    // Reset editState and close manage makes dialog
  }
}, [itemToEdit, isOpen]);
```

- Initializes the form when `itemToEdit` or `isOpen` changes.
- Cleans up local state when dialog is closed.

---

## 🛠️ **Handlers & Callbacks**

### Input Handlers

| Handler | Purpose |
|--------|---------|
| `handleInputChange(field, value)` | Updates a field in `editState`. |
| `handleCategoryChange(value)` | Updates category and resets `makeValue`. |
| `handleUnitChange(value)` | Wrapper around `handleInputChange('unitName', value)`. |
| `handleMakeChange(selectedOption)` | Updates `makeValue` in `editState`. |

### Dialog Actions

| Handler | Purpose |
|--------|---------|
| `closeDialog()` | Calls `onOpenChange(false)`. |
| `handleUpdateConfirm()` | Validates input, builds updated item, calls `onSubmitUpdate`. |
| `handleDeleteConfirm()` | Calls `onDeleteItem` with item name or ID. |

### Make Management

| Handler | Purpose |
|--------|---------|
| `handleOpenManageMakesDialog()` | Opens the nested dialog for managing makes. |
| `handleMakesManaged(newlyAssociatedMakes)` | Adds newly associated makes to the store and auto-selects one. |

---

## 🧩 **Subcomponents**

- Uses `AlertDialog` from `@/components/ui/alert-dialog` as the base modal structure.
- Renders `ReactSelect` for make selection with custom menu (`CustomMakeMenuList`) and button (`onManageMakesClick`).
- Conditionally renders `ManageCategoryMakesDialog` for managing makes per category.

---

## ✨ Summary

| Aspect | Details |
|-------|---------|
| **Type** | React Functional Component (`React.FC`) |
| **State Management** | Local (`useState`) + Derived (`useMemo`) + Side effects (`useEffect`) |
| **UI Library** | Custom UI components (`AlertDialog`, `Button`, `Input`, `Select`, etc.) |
| **Editable Fields** | Quantity, Comment, Category, Item Name, Unit, Make |
| **Actions** | Update, Delete |
| **Make Handling** | Supports dynamic make selection + management via sub-dialog |
| **Responsiveness** | Grid layout adapts to screen size |