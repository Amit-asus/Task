

# 📄 PreviousComments Component Flow Documentation

This document explains the internal logic and data flow of the `PreviousComments` React component used in the `Procurement Requests` feature. The component is responsible for displaying previous comments based on the mode (`edit` or `resolve`).

---

⸻

📌 Key Functional Blocks

1. Mode-Based Filtering
	•	Based on the mode prop, the subject filter is set:
	•	resolve → rejecting pr
	•	edit → creating pr

2. User Mapping (useUserMap)
	•	Fetches user data from "Nirmaan Users" doctype.
	•	Constructs a map to relate user IDs with their full names and avatars.

3. Comment Fetching
	•	Fetches filtered comments from "Nirmaan Comments" doctype using prId.
	•	Applies filters for:
	•	reference_name
	•	reference_doctype
	•	subject
	•	Orders results by creation timestamp (descending).

4. Conditional Rendering
	•	Loading State: Shows skeleton loader while fetching.
	•	No Comments: Returns null if no comments found.
	•	Comments Found: Renders comment cards with:
	•	Avatar
	•	Comment content
	•	Author full name
	•	Formatted timestamp

⸻

🧩 Utility Functions Used

getFullName(id: string): string

Returns full name from userMap or falls back to the ID.

getAvatar(id: string): string | undefined

Returns avatar image URL if available in userMap.

⸻

✅ Component Props

Prop	Type	Description
prId	string	ID of the procurement request
mode	'edit' | 'resolve'	Controls filtering of comments


⸻

📦 Dependencies
	•	frappe-react-sdk – for useFrappeGetDocList
	•	lucide-react – for icons
	•	@/components/ui/* – for UI primitives like Card, Avatar, Skeleton
	•	formatDate utility

⸻


