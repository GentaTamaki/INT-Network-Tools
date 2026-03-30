

# Contributing to INT Network Tools

Thank you for contributing to INT Network Tools.

To maintain documentation stability and clarity, please follow the documentation editing rules below.

---

# Documentation Editing Rules

Documentation files in this repository represent the system specification and protocol definitions.  
To avoid accidental corruption of documents, follow these rules when editing.

## 1. Do not rewrite entire files

Avoid replacing the entire content of a document.

Edits should be limited to the **minimal necessary section**.

Incorrect:

Replace the whole document with a new version.

Correct:

Modify only the section that needs changes.

---

## 2. Edit one file at a time

Do not modify multiple documentation files simultaneously.

Recommended order:

1. English document
2. Japanese document
3. Commit changes

This helps maintain consistency between language versions.

---

## 3. Confirm the target file before editing

Always verify the file path before making changes.

Example:

`docs/system/INT_System_Spec_v0.1_EN.md`

---

## 4. Modify only the intended section

When editing documentation:

- Identify the section
- Modify only that section
- Do not restructure unrelated parts

---

## 5. Commit frequently

Commit changes per file or per logical change.

Example commit message:

`Add sender transmission timing section to system specification`

Avoid large commits affecting many documents.

---

# General Philosophy

INT Network Tools documentation defines the architecture and protocol behavior of the system.

Accuracy and stability are more important than rapid changes.

Small, traceable edits are strongly preferred.