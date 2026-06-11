# Contributing to Library-First Engineering (LFE)

LFE is an open-source framework. Improvements to the protocol, the persona contracts, the skills, and the documentation are welcome. Two paths, depending on who you are:

## Path 1 — Adopters (you cloned the template for your product)

1. **Operate your project per [`USER_MANUAL.md`](USER_MANUAL.md)** — the framework runs in *your* repo; nothing needs to change here for you to use it.
2. **Keep your copy of the protocol surface read-only.** Project-specific behavior goes in the designated override sections (`## Brain Persona Overrides`, `## Retention Policy Overrides` in `LLM_AGENT_GUIDE.md`) — never as edits to the skills or protocol docs in your clone, or upstream updates become unmergeable.
3. **Found a framework bug or have an improvement?** Open an [Issue](https://github.com/StChiotis/Library-First-Engineering/issues) or start a [Discussion](https://github.com/StChiotis/Library-First-Engineering/discussions) here — that is a contribution.

## Path 2 — Framework contributors (you're improving LFE itself)

1. **Standard fork-and-PR.** Fork, branch, edit the protocol/skill/doc files directly, open a pull request. **This repository is a template and is not operated through its own pipeline** — contributions are ordinary maintenance edits, reviewed by the maintainer.
2. **Know the ownership boundary.** The agnostic core (this repo) owns the protocol: personas, assembly line, governance, and the skills as prompt documents. **Platform enforcement machinery — runtime hooks, skill-mirror tooling, eval harnesses — belongs to platform-specific distributions, not here.**
3. **Write in the framework's voice.** Agent-facing instruction text follows the positive/directive-voice convention — see the Instruction-Voice Convention in [`GOVERNANCE.md`](.docs/protocol/GOVERNANCE.md).
4. **Explain the why.** A change that is hard to reverse, surprising without context, or the result of a real trade-off gets its rationale written into the pull request description, so the decision survives without archaeology.

## License

Released under the MIT License — see [LICENSE](LICENSE).
