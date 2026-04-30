---
name: lfe-hygiene
description: Conduct a structural audit of the repository to detect "Documentation Drift" or "Spaghetti Files."
---

# LFE Hygiene Audit

## Mission
Ensure the repository remains LFE-compliant. Detect drift between the Floor Map and the physical filesystem.

## Workflow
1. **Floor Map Audit**: 
   - Read `.docs/README.md`.
   - Scan the filesystem.
   - **Identify "Illegal" Files**: Files that exist but are not registered in the Floor Map.
   - **Identify "Ghost" Files**: Files listed in the Floor Map that no longer exist.
2. **State Audit**: 
   - Verify `pipeline_status.md` reflects the current reality of the repo.
   - Check if an "active_plan.md" exists while the status is NOT "Architect" or "Builder."
3. **History Audit**: 
   - Verify `CHANGELOG.md` respects the 7-milestone rolling window.

## Output
A "Hygiene Report" listing all structural violations. If clean, output: "Repo is LFE-Compliant. Zero Drift Detected."
