# Language

Shared vocabulary for architectural suggestions. Use these terms exactly — don't substitute "component," "service," "API," or "boundary."

## Terms

**Module**
Anything with an interface and an implementation. Scale-agnostic — applies to a function, class, package, or tier-spanning slice.
_Avoid_: unit, component, service.

**Interface**
Everything a caller must know to use the module correctly. Includes the type signature, but also invariants, ordering constraints, error modes, required configuration, and performance characteristics.
_Avoid_: API, signature (too narrow).

**Implementation**
What's inside a module. Distinct from **Adapter**: a thing can be a small adapter with a large implementation (a Postgres repo) or a large adapter with a small implementation (an in-memory fake).

**Depth**
Leverage at the interface — the amount of behavior a caller can exercise per unit of interface they have to learn. **Deep** = high leverage. **Shallow** = interface nearly as complex as the implementation.

**Seam** _(from Michael Feathers)_
A place where you can alter behavior without editing in that place. The *location* at which a module's interface lives.
_Avoid_: boundary (overloaded with DDD's bounded context).

**Adapter**
A concrete thing that satisfies an interface at a seam. Describes *role*, not substance.

**Leverage**
What callers get from depth. More capability per unit of interface they have to learn.

**Locality**
What maintainers get from depth. Change, bugs, knowledge concentrate at one place rather than spreading across callers.

## Principles

- **Depth is a property of the interface, not the implementation.**
- **The deletion test.** Imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam.
- **One adapter means a hypothetical seam. Two adapters means a real one.**

## Relationships

- A **Module** has exactly one **Interface**
- **Depth** is a property of a **Module**, measured against its **Interface**
- A **Seam** is where a **Module**'s **Interface** lives
- An **Adapter** sits at a **Seam** and satisfies the **Interface**
- **Depth** produces **Leverage** for callers and **Locality** for maintainers
