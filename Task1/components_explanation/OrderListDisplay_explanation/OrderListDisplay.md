


# 📦 `OrderListDisplay` Component Documentation

This component displays categorized items in an order cart table format. It supports editing, deleting, and undoing deleted items. Categories are split into **pending** and **requested** based on their status.

---

⸻

🧩 Component Responsibilities

✅ 1. Categorizing Items
	•	pendingCategories: All categories not having status REQUEST.
	•	requestedCategories: All categories with status REQUEST.

✅ 2. Rendering Section per Category
	•	Displays category name and a table of its items.
	•	Table columns: Item, Unit, Quantity, Actions.

✅ 3. Item Row Features
	•	If item has make, it’s shown in red, inline with item name.
	•	If item has a comment, it shows a HoverCard with the comment.
	•	Buttons available:
	•	✏️ Edit: Triggers onEditItem.
	•	🗑️ Delete: Triggers onDeleteItem.

✅ 4. Undo Delete Button
	•	Appears only when canUndo is true.
	•	Clicking it triggers onUndoDelete.

⸻

📌 Props

Prop	Type	Description
procList	ProcurementRequestItem[]	List of items to be displayed
selectedCategories	CategorySelection[]	List of categories with status info
onEditItem	(item) => void	Callback when editing an item
onDeleteItem	(itemName) => void	Callback when deleting an item
canUndo	boolean	Whether the Undo button should appear
onUndoDelete	() => void	Callback for Undo Delete action


⸻

📦 Table Format

Each category table includes:
	•	Item name with optional make
	•	Hover card for comments
	•	Quantity and unit
	•	Action buttons (edit/delete)

⸻

🎨 Styling Notes
	•	Requested categories have a yellow background.
	•	Tables have borders, rounded corners, and shadow for clarity.
	•	Uses utility-first Tailwind CSS classes for layout and spacing.

⸻

