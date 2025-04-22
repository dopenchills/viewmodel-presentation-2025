---
layout: section
transition: slide-left
---

# Organize your dependencies

---

# Policy

<div class="py-4"></div>

- Clearly separate "core logic" and "peripheral logic".
  - Make "core logic" as much as possible **pure TypeScript** with **no dependencies**.
  - Assign **external dependencies** like UI frameworks to "peripheral logic".
- "Core logic" should not depend on "peripheral logic".
- "Peripheral logic" adapts to "core logic".

<div class="py-4"></div>

For example, validation logic should not depend on UI. Even if the position of the input form changes, the validation result does not change.

UI should adapt to the validation logic. If the validation result is an error, an error message should be displayed.
