# Project Domain Glossary (CONTEXT.md)

> This file defines the canonical terms for the project. These terms override any synonyms the AI might want to use. Read this at the start of any session touching documentation or core logic.

---

## 1. Documentation Architecture

### Library System
The documentation architecture is modeled as a library. 
- **Entrance Card (`pipeline_status.md`)**: The transient state of the current mission.
- **Floor Map (`.docs/README.md`)**: The lookup index for all files.
- **Shelf Indexes**: Within-file indices (e.g., the ADR table).

## 2. Logic Centralization
The principle that all core domain logic (math, business rules, or core policies) must be centralized and explicitly defined. 
- **The Definition**: The human must define which directory or module holds the "Source of Truth" for logic.
- **Rule**: AI agents are forbidden from "improvising" business logic. Logic must be derived from the documentation in `.docs/domain/`.

---

## 3. Product Terminology (Glossary)
Define the canonical terms that the AI must use.

### [Primary Entity]
**Definition**: [Describe the core object or concept of your project].

### [Secondary Entity]
**Definition**: [Describe the secondary object or concept].

---

## 3. Documentation Conventions

### CHANGELOG Rolling Window Rule
`quality/CHANGELOG.md` maintains a **7-milestone rolling window**.
- Only the 7 most recent milestones are kept in the main file.
- Older milestones are moved to `archive/changelog-history.md` by the Archivist.
