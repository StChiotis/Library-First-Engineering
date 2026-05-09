---
name: lfe-zoom-out
description: Give a high-level system map when encountering unfamiliar code. Use in the Inspector sub-pipeline as Step 1 before verification.
disable-model-invocation: false
---

# LFE Zoom Out

## Position in Pipeline
- **Phase**: 3 (Inspector sub-pipeline, Step 1)
- **Persona**: Inspector

I don't know this area of code well. Go up a layer of abstraction. Give me a map of all the relevant modules and callers, using the project's domain glossary vocabulary from `CONTEXT.md`.

Focus on:
- What this module does in the context of the whole system
- Who calls it and who it calls
- Where the seams are
- What the domain documentation says about this area
