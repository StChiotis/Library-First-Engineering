> [!NOTE]
> **Template** — populate only if your project has multiple bounded contexts.

# Context Map

> If your project has only one bounded context (most projects), use root [`CONTEXT.md`](../../CONTEXT.md) and ignore this file.
> Multi-context projects: this file lists each bounded context, where its glossary lives, and how it relates to the others.

<!--
LLM format guidance — populate when needed:

## Contexts

| Context | CONTEXT.md path | Description |
|---|---|---|
| Billing | contexts/billing/CONTEXT.md | Invoicing, payments, tax |
| Inventory | contexts/inventory/CONTEXT.md | Stock, warehouses, transfers |
| Shipping | contexts/shipping/CONTEXT.md | Carriers, routes, tracking |

## Relationships

For each pair where contexts share or translate language:

### Billing ↔ Inventory
- **Pattern:** Customer/Supplier (Inventory upstream, Billing downstream)
- **Shared kernel:** `Product`, `SKU` — same definition in both contexts
- **Translation:** Inventory's `Stock-on-hand` becomes Billing's `Available-for-invoicing` after a 24-hour reservation hold

### Billing ↔ Shipping
- **Pattern:** Separate ways
- **Term collision:** "Order" means different things — in Billing it's an invoice line, in Shipping it's a shipment unit. Treat as distinct concepts.

## Cross-context decisions

When a term is introduced and may exist in another context, /lfe-grill-with-docs prompts the Brain to classify the relationship (shared kernel / customer-supplier / translation / separate ways) and records it here.
-->
