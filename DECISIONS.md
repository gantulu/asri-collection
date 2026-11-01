# Decision Log — Asri Collection V1

> **Status:** Approved decisions recorded; this log remains open for future decisions.  
> **Version:** V1  
> **Repository:** `gantulu/asri-collection`  
> **Branch:** `docs/requirements-v1-draft`  
> **Date recorded:** 2026-11-02  
>
> This file records decisions explicitly approved by the product owner. It is the reference for why a requirement is fixed. It does not claim that the application has been implemented.

## 1. Approved Decisions

| ID | Area | Decision | Status |
| --- | --- | --- | --- |
| DEC-001 | Product scope | Customer-facing e-commerce storefront; keep V1 simple and avoid unnecessary features. | APPROVED |
| DEC-002 | Target users | General online-store visitors who purchase products. | APPROVED |
| DEC-003 | Customer access | Guest checkout only; no customer login or account registration in V1. | APPROVED |
| DEC-004 | Admin | No admin dashboard in V1. | APPROVED |
| DEC-005 | Catalogue | Product categories remain simple. | APPROVED |
| DEC-006 | Variants | Products support size and color variants. | APPROVED |
| DEC-007 | Variant pricing | Variant price is the same as the parent product price. | APPROVED |
| DEC-008 | Currency | IDR only in V1. | APPROVED |
| DEC-009 | Product data | Product catalogue is managed in Supabase Database. | APPROVED |
| DEC-010 | Inventory validation | Validate available stock during checkout/order creation. | APPROVED |
| DEC-011 | Inventory deduction | Decrease stock when the order is created. | APPROVED |
| DEC-012 | Inventory restoration | Restore stock when payment is authoritatively confirmed failed or expired. | APPROVED |
| DEC-013 | Inventory safety | Inventory movements and restoration must be idempotent; duplicate callbacks/retries must not apply the same movement twice. | APPROVED |
| DEC-014 | Shipping | Shipping cost is assigned manually per order by an authorized operator/backend process. | APPROVED |
| DEC-015 | Order tracking | Use internal order/payment status; no carrier tracking integration in V1. | APPROVED |
| DEC-016 | Payment provider | Duitku POP Sandbox only in V1; live payment processing is excluded. | APPROVED |
| DEC-017 | Payment authority | Payment creation and authoritative payment-state transitions are backend-controlled. A browser redirect is not proof of payment. | APPROVED |
| DEC-018 | Refund | Refund is performed manually outside the application; its outcome is recorded through a trusted backend operation. | APPROVED |

## 2. Decision Details and Constraints

### DEC-011 / DEC-012 — Inventory lifecycle

1. Validate stock on the backend when processing checkout/order creation.
2. Decrease stock as part of successful order creation.
3. Restore stock only after a trusted, authoritative payment result establishes failure or expiry.
4. Do not restore stock based solely on a browser return, timeout, or unverified callback.
5. Apply each inventory movement at most once, including under duplicate callbacks and retries.

### DEC-014 — Manual shipping

Shipping cost is not calculated automatically by a carrier integration. It is assigned manually per order through an authorized operator/backend process. The exact operator workflow and when the amount is finalized remain open requirements; do not invent them.

### DEC-016 / DEC-017 — Duitku payment

V1 uses Duitku POP Sandbox. Payment creation, callback validation, amount checks, idempotency, and order/payment state transitions belong to trusted backend logic. Ambiguous provider outcomes must remain recoverable/reviewable rather than being guessed as success or failure.

### DEC-018 — Manual refund

The application does not execute refunds automatically in V1. A refund is performed outside the application, and the outcome is recorded through an authorized backend operation. The operator authorization mechanism remains an open design item.

## 3. Open Decisions

These are intentionally not decided yet:

- Exact checkout fields and validation rules.
- Secure guest access to order status.
- Operator identity and authorization mechanism, without adding an admin dashboard.
- Exact manual shipping workflow and point at which shipping cost is finalized.
- Customer-facing handling of ambiguous/pending payment and recovery.
- Detailed product attributes and optional catalogue filters.
- Exact page-level UX behavior and accessibility target.

## 4. Change Control

- Approved decisions must not be silently changed by an AI agent.
- A change requires an explicit product-owner decision and an update to this log.
- When a decision changes, retain the old entry and mark it `SUPERSEDED`; add a new decision ID rather than rewriting history.
- A technical recommendation is not an approved decision until the product owner confirms it.
- Documentation status does not prove implementation status.
