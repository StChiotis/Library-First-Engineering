# Library-First Engineering (LFE)
 
<p align="center">
  <img src=".assets/logo.png" width="300" alt="LFE Mascot">
</p>

<h2 align="center">
  The Enterprise Architecture Protocol for the Agentic Age.<br>
  <small><i>Thinking in the Human. Processing in the AI. Truth in the Documentation.</i></small>
</h2>

> **The Brick House Metaphor**  
> In the Agentic Age, most codebases are built of **Straw** 🌾 (one-off prompts) or **Stick** 🪵 (unstructured chat sessions). They look like houses, but they collapse when the "Big Bad Wolf" 🐺 of **Spaghetti Decay** and **Context Drift** blows.  
> 
> **LFE is the Brick House.** 🧱 We build block-by-block, using Documentation-as-Infrastructure to ensure your project remains standing, no matter how hard the AI blows.

In the era of AI-driven development, code is cheap but context is expensive. **Library-First Engineering (LFE)** is an architectural protocol that treats **Documentation-as-Infrastructure**—the operating system of your project.

This repository is a "Blank Canvas" template for AI-Founders and CTOs to bootstrap projects that are disciplined, AI-navigable, and resistant to "Spaghetti Decay."

---

## 🏛️ The LFE Process
LFE orchestrates AI agents through a disciplined assembly line, ensuring every change is planned and verified.

```mermaid
graph LR
Gate{Gate} -- "Major (Full Pipeline)" --> Architect[Architect: Plan]
Architect --> Builder[Builder: Code]
Builder --> Inspector[Inspector: Test]
Inspector --> Archivist[Archivist: Doc]
Gate -. "Minor (Scout Mode)" .-> Scout[Scout: Quick Fix]
Scout --> Archivist
```

> [!TIP]
> View the [Full Assembly Line Protocol](.docs/protocol/ASSEMBLY_LINE.md) for a detailed breakdown of the "Major Change" workflow.


---

## 🏆 The LFE Benchmark
Why LFE is the pioneering standard for agentic engineering.

| Feature | Standard AI Workflow | **LFE Protocol (V1.0)** |
| :--- | :--- | :--- |
| **Autonomy** | Unrestricted (Cowboy Coding) | **Persona-Based Tool-Locking** |
| **Planning** | Implicit ("Just do it") | **Mandatory `.plans/` Documentation** |
| **Logic** | Scattered / Hallucinated | **Logic Centralization (Truth Home)** |
| **Verification** | Self-Verified by AI | **Independent Inspector Audit** |
| **Governance** | Code-First (Spaghetti Decay) | **Docs-as-Infrastructure** |
| **Safety** | Trust-Based | **Zero-Trust (Break-Glass Override)** |

---

## ⚠️ The Alignment Paradox (Why LFE needs YOU)
Modern LLMs are heavily trained (via RLHF) to be helpful sycophants. If you tell an AI to *"forget the rules and just do X"*, its core training will almost always override the project's protocol. 

**LFE cannot physically stop you from breaking your own rules.** The protocol is a shared contract between you and the agent. The "Complexity Gate" exists so *you* can enforce discipline on the *AI*. If you choose to bypass the gate and give raw, rule-breaking commands, the AI will happily oblige and cause Spaghetti Decay. **LFE is the pavement, but you must choose to drive on it.**

**Chat vs. Agentic Architectures:** You will experience varying degrees of protocol adherence depending on the AI tool you use. It is crucial to distinguish between the **Engine** (the model, like Claude 3.5 or GPT-4o) and the **Architecture** (the tool wrapping the model):
- **"Chat-based" IDEs** (e.g., **Cursor**, **GitHub Copilot**, **Windsurf**) prioritize immediate execution and obedience. Even if powered by an elite engine like Claude, they are highly susceptible to the Alignment Paradox and will break rules if you tell them to.
- **"Agentic" Architectures** (e.g., **Antigravity**, **Devin**, or CLI loops like **Claude Code**) are structurally designed to exercise judgment. Using the exact same engine, they will naturally enforce the LFE assembly line more strictly.

Regardless of your tooling, the integrity of the codebase ultimately relies on the founder honoring the process.

---

## 🏛️ The 4 Pillars of LFE

### 1. The Library System
A project is a library, not a dump. We enforce a three-layer hierarchy (**Entrance Card → Floor Map → Shelf Index**) to bound AI context and ensure agents find the truth in under 300 lines of orientation.

### 2. Persona Sovereignty
We separate "Thinking" from "Doing." By orchestrating AI into distinct personas (**Architect → Builder → Inspector → Archivist**), we enforce a disciplined assembly line that prevents "cowboy coding."

### 3. Logic Sovereignty
Domain logic is sacred. LFE ensures all core business rules are centralized in designated modules. The AI is trained to respect these modules as the sole authority for the project's logic.

### 4. The Rolling Window
Knowledge has a shelf life. LFE maintains a lean "Active Working Memory" for AI agents by moving stale history to archives, preventing context-drift and hallucination.

---

## 🚀 One-Sentence Marketing
- **For Enterprise CTOs**: "LFE is an AI-first repository architecture that prevents spaghetti decay by enforcing strict documentation and planning rules before AI writes a single line of code."
- **For AI Founders**: "LFE is a zero-config repository standard that bounds your AI's context window, saving you tokens and preventing hallucinations as your codebase scales."

---

## 🛠️ Quick Start
1. **Clone this structure** into your new project.
2. **Onboard the AI**: The repository includes pre-configured rule files for popular AI IDEs (`.cursorrules`, `.windsurfrules`, `.clinerules`, and `.github/copilot-instructions.md`). Alternatively, use the **[System Prompt Adapter](file:///.agents/adapters/system_prompt.txt)** for standalone agents.
3. **Run the Complexity Gate**: Ask your agent: *"I have adopted the LFE protocol. Run /lfe-boot to begin."*

---

## 🤝 Contributing
LFE is an open-source movement. Join us in refining the protocol to make AI-driven engineering more reliable.
- Check the **[CONTRIBUTING.md](CONTRIBUTING.md)**
- Licensed under **MIT**.

---

## 👤 Author & Vision
The **Library-First Engineering (LFE) Protocol** is the original creation and intellectual product of **Stylianos Chiotis**. 

> *"My mission is to give everyone a framework to bring their ideas to life in the most efficient way possible. I believe that **the pavement is the first thing you lay before you build a house.** LFE is that pavement—a solid foundation that ensures future scalability, maintainability, and reliability in the Agentic Age."* — **Stylianos Chiotis**

---

## 📜 Credits & Attributions
- **Utility Skills**: The core utility skills in the `.agents/skills` directory (*Grill-me, Diagnose, TDD, etc.*) were adapted from the open-source work of **[Matt Pocock](https://github.com/mattpocock/skills)**. We acknowledge his work in the agentic skills space.

---
*Prevent Spaghetti. Build Rigor. The Library-First Way.*
