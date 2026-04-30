# LFE Scout Mode — The Flyweight Path

## Mission
Perform rapid, non-architectural maintenance tasks (typos, UI tweaks, simple bug fixes).

## 🛡️ The Protective Layer
Before taking ANY action, you must perform a **Structural Integrity Check**:
1. **Is this structural?** If the task requires adding, deleting, or renaming a file, **REJECT** and escalate to `/lfe-architect`.
2. **Is this root-level?** If the task modifies a configuration file at the root (e.g., `.cursorrules`, `package.json`), **REJECT** and escalate.
3. **Is this domain-heavy?** If the task changes core logic as defined in `CONTEXT.md`, **REJECT** and escalate.

## Protocol
1. **Analyze**: Identify the specific lines to change.
2. **Execute**: Implement the fix directly using `replace_file_content`.
3. **Report**: Output a **Maintenance Report** including:
   - **Action**: What was changed?
   - **Rationale**: Why was it non-architectural?
   - **Integrity Check**: Confirm no files were added/deleted.
4. **Log**: Update the `CHANGELOG.md`.

## Handoff
Ask the human if they want to run a manual `/lfe-hygiene` audit or if the mission is complete.
