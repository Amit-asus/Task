
---

# `NewItemDialog` Component

The `NewItemDialog` is a **React Functional Component** used to allow users to either:
- **Create a new item** (by saving it to the backend), or
- **Request an item** (without creating it immediately).

It supports features like:
- Selecting a category.
- Entering item name, unit, and quantity.
- Searching for existing items via fuzzy search.
- Creating new items if allowed by the selected category.

---

## 🎯 Purpose

This dialog enables users to:
1. Choose a **category** for the new item.
2. Enter or select an **item name** (with suggestions from existing items).
3. Set a **unit** and **quantity**.
4. Add a **comment** (optional).
5. Either:
   - Create a new item (if creation is enabled for the category), or
   - Request the item (if creation is disabled).

---

## 🔌 Inputs / Props

### Interface: `NewItemDialogProps`

```ts
interface NewItemDialogProps {
  isOpen: boolean;
  onOpenChange: (isOpen: boolean) => void;
  categories: Category[];
  workPackage: string;
  onSubmit: (itemData: ProcurementRequestItem, isRequest: boolean) => void;
  fuzzySearch: (input: string) => FuseResult<Items>[];
  itemList?: Item[];
  itemMutate: () => Promise<any>;
}
```

| Prop | Type | Description |
|------|------|-------------|
| `isOpen` | `boolean` | Controls whether the dialog is visible. |
| `onOpenChange` | `(isOpen: boolean) => void` | Callback when dialog open state changes. |
| `categories` | `Category[]` | List of available categories for selection. |
| `workPackage` | `string` | Name of the associated work package (for display only). |
| `onSubmit` | `(itemData: ProcurementRequestItem, isRequest: boolean) => void` | Called when user creates or requests an item. |
| `fuzzySearch` | `(input: string) => FuseResult<Items>[]` | Function to search for similar existing items. |
| `itemList` | `Item[]` | Optional list of all items for fuzzy search results. |
| `itemMutate` | `() => Promise<any>` | Function to refresh item list after creation. |

---

## 🔄 Outputs / Events

The component triggers one main event:

### ✅ `onSubmit(updatedItem, isRequest)`
- Fired when the user clicks **"Create & Add"** or **"Request Item"**.
- Returns:
  - `updatedItem`: The newly created or requested item object.
  - `isRequest`: Boolean indicating if this is a request (`true`) or a created item (`false`).

---

## 🧠 Internal State Management

### Local State

Defined using `useState`:

```ts
const [selectedCategory, setSelectedCategory] = useState<SingleValue<CategoryOption>>(null);
const [newItem, setNewItem] = useState<NewItemState>(initialNewItemState);
const [fuzzyMatches, setFuzzyMatches] = useState<Fuse.FuseResult<Item>[]>([]);
const [isFocused, setIsFocused] = useState(false); // Track focus for fuzzy list
```

#### `NewItemState` Interface

```ts
interface NewItemState {
    itemName: string;
    unitName: string;
    quantity: string; // Keep as string for input control
    comment: string;
}
```

- Holds temporary values entered during form input.
- Resets when dialog closes or category changes.

#### `selectedCategory`
- Stores the currently selected category object.
- Used to determine if item creation is enabled.

#### `fuzzyMatches`
- Stores search results from the fuzzy search function based on the entered item name.

#### `isFocused`
- Tracks focus state of the item name input field to show/hide fuzzy search results.

---

### 💡 Derived State (via `useMemo`)

#### `catOptions`
- Transforms raw `categories` into options suitable for `react-select`.
- Includes computed flags like `newItemsDisabled`.

#### `isNewItemsDisabled`
- Boolean derived from `selectedCategory.newItemsDisabled`.

---

### 🔄 Effects (via `useEffect`)

```ts
// Reset form when dialog opens
useEffect(() => { ... }, [isOpen]);

// Reset item details when category changes
useEffect(() => { ... }, [selectedCategory]);
```

- Ensures clean UI state when opening/closing or changing category.

---

## 🛠️ Handlers & Callbacks

| Handler | Purpose |
|--------|---------|
| `handleInputChange(field, value)` | Updates a field in `newItem`. |
| `handleUnitChange(value)` | Wrapper around `handleInputChange('unitName', value)`. |
| `closeDialog()` | Calls `onOpenChange(false)` to close the dialog. |
| `handleSelectExistingItem(item)` | Adds an existing item to the procurement list. |
| `handleConfirm(isRequest)` | Validates input and submits a new item or request. |

---

## 🧩 Subcomponents

- Uses `AlertDialog` from `@/components/ui/alert-dialog` as the modal container.
- Renders `ReactSelect` for category selection.
- Displays `SelectUnit` for selecting units.
- Shows fuzzy search results dynamically below the item name input.

---
