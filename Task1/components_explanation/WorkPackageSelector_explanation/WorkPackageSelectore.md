

WorkPackageSelector Component

The WorkPackageSelector is a React Functional Component used to display a grid of work packages. Each work package is shown inside a clickable card, and users can select a work package by clicking on the card.

⸻

📦 Props

interface WorkPackageSelectorProps {
  wpList?: ProcurementPackages[]; // List of work packages to display
  onSelectWP: (wpName: string) => void; // Callback when a work package is selected
  isLoading?: boolean; // Flag to indicate if data is loading
}


⸻

🔁 Component Logic
	1.	Loading State
If isLoading is true, a loading message is displayed.
	2.	Empty State
If wpList is empty or undefined, a message saying “No Work Packages available” is shown.
	3.	Render Grid
If work packages are available:
	•	Display a responsive grid.
	•	Each item is rendered in a Card with:
	•	An image (fallback to placeholder if not provided)
	•	The work package name
	•	The card is clickable, and triggers onSelectWP with the selected work package name.

⸻

🖼️ UI Features
	•	Responsive grid layout (2 - 6 columns based on screen size).
	•	Hover effect for better UX.
	•	Image is lazily loaded for performance.
	•	Text wraps correctly to avoid overflow.
	•	Fallback image used if the actual image is not provided.

⸻




