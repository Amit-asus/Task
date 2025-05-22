# `ActionButtons` Component 

---

## 🎯 Purpose

The `ActionButtons` component is a **UI element** that provides action buttons for different modes in the procurement request workflow:

- **Create mode**: Submit a new procurement request.
- **Edit mode**: Update an existing (draft) procurement request.
- **Resolve mode**: Resolve a previously rejected request and resubmit it.

It includes:
- A confirmation dialog to prevent accidental submissions.
- An optional comment input field for final notes before submission.
- Loading indicators during submission.

---

## 🔌 Inputs / Props

```ts
interface ActionButtonsProps {
    mode: 'create' | 'edit' | 'resolve';
    onSubmit: () => Promise<void>;
    isSubmitting: boolean;
    disabled?: boolean;
    comment?: string;
    onCommentChange?: (comment: string) => void;
}
```

| Prop | Type | Description |
|------|------|-------------|
| `mode` | `'create' \| 'edit' \| 'resolve'` | Determines which action mode the button should display. |
| `onSubmit` | `() => Promise<void>` | Callback triggered when user confirms action. Usually submits or updates the PR. |
| `isSubmitting` | `boolean` | Indicates if a submission is in progress (disables button and shows spinner). |
| `disabled` | `boolean` | Optional — disables the button (e.g., if no items are added). |
| `comment` | `string` | Optional — current comment text passed from parent (to sync with internal state). |
| `onCommentChange` | `(comment: string) => void` | Optional — callback to update comment in parent/store when edited in dialog. |

---

## 🔄 Outputs / Events

### ✅ Button Click → Confirmation Dialog
When the user clicks the action button, a confirmation dialog appears asking them to confirm the action.

### ✅ Confirm Submission
If the user confirms:
- Triggers the `onSubmit()` function.
- If `onCommentChange` is provided, the updated comment is passed back to the parent before submitting.

---

## 🧠 Internal State Management

This component uses local state for:

### 1. Dialog Visibility
```ts
const [isDialogOpen, setIsDialogOpen] = useState(false);
```
Controls whether the confirmation dialog is open or closed.

### 2. Local Comment
```ts
const [localComment, setLocalComment] = useState(comment ?? '');
```
Maintains a copy of the comment input within the dialog. Synced with prop via:

```ts
useEffect(() => {
    setLocalComment(comment ?? '');
}, [comment]);
```

> This ensures that the internal comment box reflects any changes made outside the component (e.g., from a store or parent form).

---

## 🛠️ Handlers & Callbacks

### `handleConfirm`
```ts
const handleConfirm = async () => {
    if(onCommentChange) {
        onCommentChange(localComment); // Update parent/store comment
    }
    await onSubmit(); // Submit the action
};
```
- Updates the comment in the parent component if `onCommentChange` is provided.
- Calls the `onSubmit` handler to perform the actual action.

---

## 📦 Dependencies

| Dependency | Usage |
|-----------|-------|
| `react` | Core React library |
| `@/components/ui/button`, `alert-dialog` | UI components for buttons and modal dialog |
| `lucide-react` | Icons (`ListChecks`, `CheckCheck`) |
| `react-loader-spinner` | Spinner animation while submitting |

---

## 🧩 Summary Table

| Feature | Description |
|--------|-------------|
| **Type** | Functional React Component (`React.FC`) |
| **State Management** | Uses local state (`useState`) + prop synchronization (`useEffect`) |
| **UI Elements** | AlertDialog, Button, Textarea |
| **Key Actions** | Shows confirmation dialog, handles comment input, triggers submission |
| **Responsiveness** | Responsive design using Tailwind CSS classes |
| **Validation** | Disables button based on `disabled` and `isSubmitting` flags |

---