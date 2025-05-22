### 🧑 Explanation of the `NewProcurementRequestPage` Component

The **`NewProcurementRequestPage`** is a **main page component** in a procurement system where users can create, edit, or resolve **procurement requests** — which are basically lists of items someone wants to buy or request for a project.

---

### 🔧 What Does It Do?

This page helps users go through the **entire process** of making a procurement request:

1. **Select a Work Package**
   - Users pick a **work package**, which is like a category or section of a project (e.g., "Tools & Equipment").
   - This sets up the context for what kind of items they'll be adding.

2. **Add Items**
   - Users can add items to their request.
   - If allowed, they can **create a brand-new item** (like adding it to the system).
   - If not, they can just **request an item** without creating it yet.

3. **Edit or Delete Items**
   - Once added, users can **edit** any item (change quantity, unit, make, etc.) or **delete** it if needed.

4. **Submit or Update Request**
   - When done, users can **submit** the full list as a new request or **update** an existing one.
   - If editing a draft, they can also cancel the request if needed.

5. **Resolve Requests**
   - In some cases, users may need to **resolve** a request — meaning finalize or close it after action has been taken.

---

### ⚙️ Why Is It Used?

This component brings together all the tools and data needed to:
- Help users **build a procurement list** from scratch.
- Allow them to **manage items** (add, edit, delete) with ease.
- Handle complex logic like **categories**, **makes**, and **item creation rules**.
- Support **different modes**: creating a new request, editing a draft, or resolving a completed request.

It uses a **centralized store** (Zustand) to keep track of everything — selected work package, added items, comments, etc. — so the user doesn't lose data while navigating through steps.

---

---

# 🔄 Component Communication & Data Flow

This document outlines how data flows between components in the `NewProcurementRequestPage`, including:
- Prop passing (parent → child)
- Event callbacks (child → parent)
- Shared state via hooks/store
- API calls and mutations

---

## 🧩 Main Components Involved

| Component | Description |
|----------|-------------|
| `NewProcurementRequestPage` | Main page component. Orchestrates work package selection, item management, and submission. |
| `WorkPackageSelector` | Lets user select a work package. |
| `ItemSelectorControls` | Controls for adding new items or requests. |
| `OrderListDisplay` | Displays list of selected procurement items. |
| `ActionButtons` | Handles final actions like submit, cancel, resolve. |
| `PreviousComments` | Shows previous comments in edit/resolve mode. |
| `NewItemDialog` | Dialog to create/request a new item. |
| `EditItemDialog` | Dialog to edit/delete an existing item. |

---

## 📦 State Management

### 🔁 Zustand Store: `useProcurementRequestStore`
- Centralized state for:
  - Selected Work Package (`selectedWP`)
  - Procurement List (`procList`)
  - Selected Categories (`selectedCategories`)
  - Undo Stack (`undoStack`)
  - New Comment (`newPRComment`)
  - Makes per category (`initialCategoryMakes`)
- Accessed and updated by multiple components.
- Reset and initialization handled from `NewProcurementRequestPage`.

---

## 📥 Data Fetching

### 🚀 Hooks Used:

| Hook | Purpose |
|------|---------|
| `useFrappeGetDoc` | Fetches existing PR data when editing/resolving. |
| `useProcurementRequestData` | Fetches project, WP list, categories, items, makes. |
| `useProcurementRequestForm` | Provides business logic and callbacks for form interactions. |
| `useSubmitProcurementRequest` | Handles form submission, update, resolve, and cancellation. |

### 💾 API Mutations
- `itemMutate`: Refresh item list after creation.
- `makeListMutate`: Refresh make list after changes.
- `categoryMakeListMutate`: Update category-make associations.

---

## 🔁 Data Flow Overview

### 1. **Work Package Selection**
#### From: `NewProcurementRequestPage`  
#### To: `WorkPackageSelector`

```tsx
<WorkPackageSelector
  wpList={wpList}
  onSelectWP={handleWorkPackageSelect}
/>
```

- **Prop**: `wpList` — List of available work packages.
- **Callback**: `onSelectWP(wpName)` — Triggers when user selects a work package.
- **Effect**: Derives initial makes map and updates store.

---

### 2. **Item Addition**
#### From: `NewProcurementRequestPage`  
#### To: `ItemSelectorControls`

```tsx
<ItemSelectorControls
  selectedWP={selectedWP}
  itemOptions={itemOptions}
  allMakeOptions={allMakeOptions}
  selectedCategories={selectedCategories}
  onAddItem={addOrUpdateItem}
  onOpenNewItemDialog={() => setShowNewItemDialog(true)}
  updateCategoryMakesInStore={updateCategoryMakes}
/>
```

- **Prop**: `selectedWP`, `selectedCategories`, etc.
- **Callback**: `onAddItem(item)` — Adds new item to the list.
- **Callback**: `onOpenNewItemDialog()` — Opens `NewItemDialog`.

---

### 3. **Item Display & Editing**
#### From: `NewProcurementRequestPage`  
#### To: `OrderListDisplay`

```tsx
<OrderListDisplay
  procList={procList}
  selectedCategories={selectedCategories}
  onEditItem={(item) => setItemToEdit(item)}
  onDeleteItem={deleteItemFromList}
  canUndo={undoStack.length > 0}
  onUndoDelete={undoLastDelete}
/>
```

- **Props**: `procList`, `selectedCategories`
- **Callbacks**:
  - `onEditItem(item)` — Opens `EditItemDialog`.
  - `onDeleteItem(itemName)` — Removes item from list.
  - `onUndoDelete()` — Restores last deleted item.

---

### 4. **Adding New Items**
#### From: `NewItemDialog`  
#### To: `useProcurementRequestForm.addOrUpdateItem()`

```tsx
<NewItemDialog
  isOpen={showNewItemDialog}
  onOpenChange={setShowNewItemDialog}
  categories={categoryList}
  workPackage={wpFromStore}
  onSubmit={addOrUpdateItem}
/>
```

- **Prop**: `categories`, `workPackage`
- **Callback**: `onSubmit(item, isRequest)` — Adds new item to store.

---

### 5. **Editing Existing Items**
#### From: `EditItemDialog`  
#### To: `useProcurementRequestForm.updateItemInList()`

```tsx
<EditItemDialog
  isOpen={!!itemToEdit}
  onOpenChange={(isOpen) => { if (!isOpen) setItemToEdit(null); }}
  itemToEdit={itemToEdit}
  categories={categoryList}
  onSubmitUpdate={updateItemInList}
  onDeleteItem={deleteItemFromList}
  // Make-related props
  allMakeOptions={allMakeOptions}
  initialCategoryMakes={initialCategoryMakes}
  selectedCategories={selectedCategories}
  updateCategoryMakesInStore={updateCategoryMakes}
/>
```

- **Props**: Full access to category/make data for editing.
- **Callbacks**:
  - `onSubmitUpdate(updatedItem)` — Updates item in store.
  - `onDeleteItem(itemName)` — Deletes item from store.

---

### 6. **Final Actions**
#### From: `NewProcurementRequestPage`  
#### To: `ActionButtons`

```tsx
<ActionButtons
  mode={mode}
  onSubmit={handleSubmitAction}
  isSubmitting={isSubmitting}
  disabled={procList.length === 0 || isSubmitting}
  comment={newPRComment}
  onCommentChange={setComment}
/>
```

- **Props**: Submission mode, loading state, comment.
- **Callback**: `onSubmit()` — Submit/update/resolve PR.
- **Callback**: `onCommentChange(comment)` — Update comment in store.

---

## 📡 API Interactions

| Action | Method | Endpoint | Triggered By |
|-------|--------|----------|--------------|
| Load Project/PR Data | GET | `/api/method/frappe.client.get` | `useFrappeGetDoc` on load |
| Create Item | POST | `/api/resource/Items` | `useFrappeCreateDoc` in `NewItemDialog` |
| Submit PR | POST | `/api/resource/Procurement Requests` | `submitNewPR()` |
| Resolve/Edit PR | PUT | `/api/resource/Procurement Requests/:id` | `resolveOrUpdatePR()` |
| Cancel Draft | PUT | `/api/resource/Procurement Requests/:id` | `cancelDraftPR()` |

---

## 🧠 Summary Table

| Interaction | Direction | Mechanism |
|------------|-----------|-----------|
| Select Work Package | Parent → Child | Prop + Callback |
| Add New Item | Child → Parent | Dialog callback → Store |
| Edit/Delete Item | Child ↔ Parent | Dialog open/close + Store update |
| Submit Request | Parent → Child | Callback + Store |
| Make Management | Nested Dialog | Props + Callbacks through `EditItemDialog` |

---