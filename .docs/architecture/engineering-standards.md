# Engineering Standards

> [!NOTE]  
> **Template File:** This is a blank canvas for your project. Replace the placeholders below with your own engineering principles and tooling choices.

Define your coding conventions, linting rules, and PR requirements here.

## 🎨 Design Principles
<!-- Example:
- **Immutability First**: State should not be mutated. Use readonly data structures.
- **Fail Fast**: Throw errors at the boundary, do not silently swallow them.
-->
- [Your Principle 1]
- [Your Principle 2]

## 🛠️ Tooling
<!-- Example:
- **Linting**: ESLint with Airbnb config.
- **Formatting**: Prettier on commit.
-->
- [Your Tool 1]
- [Your Tool 2]

## Domain/Boundary Separation

> Framework default. Define for your project type below.

- **Core (stable, owned by the project):** _<fill in: e.g., domain models, business rules, calculation logic>_
- **Boundary (volatile, depends on external systems):** _<fill in: e.g., HTTP handlers, UI components, database adapters, message-bus glue>_
- **Rule:** changes to `boundary` should not require changes to `core`. If they do, the boundary is leaking.

**Common project-type defaults** (delete the rows that don't apply):

| Project type | Core looks like | Boundary looks like |
|---|---|---|
| Web app | Domain models, business rules | UI components, DOM, API handlers |
| CLI tool | Command logic | Argument parsing, terminal I/O, exit codes |
| Library | Public API behavior | Adapter shims, config loading |
| Data pipeline | Transformations, business rules | Source/sink connectors, scheduler glue |
| Microservice | Domain aggregates | HTTP/gRPC handlers, message-bus adapters |
