

# Contributing to INT Network Tools

Thank you for contributing to INT Network Tools.

To maintain documentation stability and clarity, please follow the documentation editing rules below.

---

# Documentation Editing Rules


Documentation files in this repository represent the system specification and protocol definitions.  
To avoid accidental corruption of documents, follow these rules when editing.

## 0. Explicitly identify the file before editing

Before proposing or applying any modification, clearly state the **exact file path** that will be edited.

Example:

`docs/system/INT_System_Spec_v0.1_EN.md`

Editing must not proceed until the target file has been explicitly identified and confirmed.

After the file is identified, confirmation to proceed with the edit must be obtained before making any modification.

This step is required before applying the rules below.

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

## 6. Confirm changes before continuing

After any modification is applied, confirm that the change was correctly implemented before proceeding with additional edits.

Recommended workflow:

1. Identify the change to be made
2. Apply the minimal modification
3. Confirm the result (build / render / behavior / document content)
4. Commit the confirmed change
5. Only then proceed to the next modification

Do not propose or apply multiple follow‑up changes before the result of the current modification has been verified.

This rule applies to documentation, software, hardware, and specification development in this repository.

---

# General Philosophy

INT Network Tools documentation defines the architecture and protocol behavior of the system.

Accuracy and stability are more important than rapid changes.

Small, traceable edits are strongly preferred.