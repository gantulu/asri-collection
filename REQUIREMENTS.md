# Requirements — Asri Collection V1

> **Status:** DRAFT — awaiting review and approval  
> **Version:** V1  
> **Repository:** `gantulu/asri-collection`  
> **Branch:** `docs/requirements-v1-draft`  
> **Last updated:** 2026-11-02  
>
> This document is the working product-requirements baseline. It is not an implementation claim, database schema, API contract, or authorization to expand scope. Requirements marked as approved reflect decisions already made; any unresolved detail remains open until explicitly decided.

## 1. Purpose

Asri Collection V1 is a modern, responsive e-commerce storefront that allows customers to discover products, manage a cart, place orders as guests, and complete payment through Duitku POP Sandbox.

The V1 implementation uses React, Vite, and TypeScript on the frontend, with Supabase PostgreSQL and Supabase Edge Functions for trusted backend operations.

## 2. Product Goals

- Provide a clear, mobile-first shopping experience with desktop support.
- Allow customers to browse products and categories without creating an account.
- Support product variants for size and color.
- Provide cart and guest-checkout flows.
- Validate prices and stock on the backend before creating an order.
- Initiate and track payments through Duitku POP Sandbox.
- Show customers reliable internal order and payment status.
- Keep operationally sensitive actions under backend control.
- Keep V1 focused; do not introduce an admin dashboard or automated shipping/refund systems.

## 3. V1 Scope

### 3.1 Included

- Storefront home page.
- Product listing and simple category filtering.
- Product detail and variant selection.
- Shopping cart.
- Guest checkout.
- Order creation and internal order-status tracking.
- Manual shipping-cost assignment per order by an authorized operator/backend process.
- Duitku POP Sandbox payment initiation and callback processing.
- Payment-result and order-status experiences.
- Backend-controlled inventory and payment lifecycle handling.
- Manual refund outcome recording through the backend.

### 3.2 Explicitly excluded

- Customer registration, login, and account management.
- Admin dashboard.
- Automated shipping-rate calculation or carrier integration.
- Automated refund execution inside the application.
- Duitku production/live payment processing.
- Multi-currency support.
- Variant-specific pricing.
- Customer-facing shipment tracking integration.

## 4. Functional Requirements

### 4.1 Storefront and Catalogue

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| FR-001 | Customers can view the storefront home page. | Must | Draft |
| FR-002 | Customers can browse the product catalogue. | Must | Draft |
| FR-003 | Customers can filter products by simple category. | Must | Draft |
| FR-004 | Customers can open a product detail page. | Must | Draft |
| FR-005 | Product data is managed in Supabase Database. | Must | Approved |
| FR-006 | A product may have size and color variants. | Must | Approved |
| FR-007 | Variant price is inherited from the parent product; variant-specific pricing is not supported in V1. | Must | Approved |
| FR-008 | Product availability and price shown by the client are informational and must be revalidated by the backend during checkout. | Must | Approved |

### 4.2 Cart

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| FR-009 | Customers can add a selected product/variant to the cart. | Must | Draft |
| FR-010 | Customers can change item quantities and remove cart items. | Must | Draft |
| FR-011 | The cart displays item quantities and a subtotal. | Must | Draft |
| FR-012 | The cart must not be treated as an inventory reservation. | Must | Draft |
| FR-013 | The backend recalculates authoritative prices and validates stock at checkout. | Must | Approved |

### 4.3 Guest Checkout and Orders

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| FR-014 | Customers can check out without creating or signing into an account. | Must | Approved |
| FR-015 | Checkout collects the customer and fulfilment information required to process the order. | Must | Draft |
| FR-016 | The backend validates checkout input, product prices, variants, and available stock. | Must | Approved |
| FR-017 | The backend creates the order and its item snapshots. | Must | Approved |
| FR-018 | Shipping cost is assigned manually per order by an authorized operator/backend process. | Must | Approved |
| FR-019 | Customers can retrieve and view their order status through the supported order-status flow. | Must | Approved |
| FR-020 | Order history must preserve transaction-time product and price snapshots even if catalogue data changes later. | Must | Approved |

### 4.4 Inventory

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| FR-021 | Stock is validated when checkout/order creation is processed. | Must | Approved |
| FR-022 | Stock is decreased when the order is created. | Must | Approved |
| FR-023 | Stock is restored when payment is authoritatively determined to be failed or expired. | Must | Approved |
| FR-024 | Inventory changes and restoration must be duplicate-safe; repeated callbacks or retries must not apply the same movement more than once. | Must | Approved |
| FR-025 | Inventory movements must be traceable through explicit movement records/types. | Must | Approved |

### 4.5 Payment

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| FR-026 | V1 uses Duitku POP Sandbox only. | Must | Approved |
| FR-027 | Payment creation is initiated by a trusted backend operation. | Must | Approved |
| FR-028 | Duitku callbacks are validated before payment/order state is changed. | Must | Approved |
| FR-029 | Duplicate callbacks and payment-creation retries are handled idempotently. | Must | Approved |
| FR-030 | A browser redirect or return URL alone must never be treated as proof of successful payment. | Must | Approved |
| FR-031 | Payment states must support pending and recoverable/ambiguous outcomes; uncertain provider outcomes must not be guessed. | Must | Approved |
| FR-032 | A callback received while payment creation is still in progress must be handled through a deferred/recoverable flow. | Must | Approved |
| FR-033 | Payment and order transitions must be validated by backend-controlled rules. | Must | Approved |

### 4.6 Refunds and Operator Actions

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| FR-034 | Refund execution is manual and performed outside the application in V1. | Must | Approved |
| FR-035 | The refund outcome is recorded through a trusted backend operation. | Must | Approved |
| FR-036 | Operator-only actions require backend authorization. | Must | Approved |
| FR-037 | Refund evidence, if stored, must use private storage access rather than public URLs. | Should | Approved |

## 5. Non-Functional Requirements

| ID | Requirement | Priority | Status |
| --- | --- | --- | --- |
| NFR-001 | The storefront must be responsive and designed mobile-first, with desktop support. | Must | Draft |
| NFR-002 | User-facing flows must provide clear loading, empty, validation, error, and success states. | Must | Draft |
| NFR-003 | Backend operations are authoritative for prices, stock, order creation, and payment status. | Must | Approved |
| NFR-004 | Privileged operations and payment integration must execute on the backend, not in browser code. | Must | Approved |
| NFR-005 | Secrets, service-role credentials, and Duitku signing credentials must never be exposed in frontend bundles or committed to the repository. | Must | Approved |
| NFR-006 | Untrusted checkout and callback inputs must be validated server-side. | Must | Approved |
| NFR-007 | Critical order, inventory, and payment operations must be safe against duplicate requests and retries. | Must | Approved |
| NFR-008 | Sensitive provider response data and personal/payment data must not be retained or logged unnecessarily. | Must | Approved |
| NFR-009 | Database access, storage access, and operator actions must follow least-privilege principles. | Must | Approved |
| NFR-010 | The application must use IDR as its only supported currency in V1. | Must | Approved |

## 6. Approved Technology Constraints

- Frontend: React + Vite + TypeScript.
- Backend: Supabase PostgreSQL + Supabase Edge Functions.
- Client routing: React Router.
- Client state: Zustand.
- Server state: TanStack Query.
- Runtime validation: Zod.
- Payment provider: Duitku POP Sandbox.
- Currency: IDR only.

These are target-stack requirements. Exact package versions, installed dependencies, and implemented modules must be verified from repository files when implementation exists.

## 7. User-Facing Pages

The intended V1 pages are:

| Page | Purpose |
| --- | --- |
| Home | Storefront landing page and product discovery |
| Products | Product catalogue and category filtering |
| Product Detail | Product information, variant selection, add-to-cart |
| Cart | Review and update cart items |
| Checkout | Guest order submission and payment initiation |
| Order Status | View backend-verified order/payment status |
| Payment Result | Explain the payment return state and direct the customer to verified order status |

Exact URL paths are intentionally unspecified until the router is designed and implemented.

## 8. Business Rules and Invariants

1. Guest checkout only; no customer account is required in V1.
2. Currency is IDR only.
3. Product catalogue data is managed in Supabase Database.
4. Product categories are simple.
5. Product variants are size and color; variant price equals parent product price.
6. Stock is checked at checkout and decreased when the order is created.
7. Stock is restored only after a backend-verified failed or expired payment outcome.
8. Inventory movements and payment processing must be duplicate-safe.
9. Shipping cost is manually assigned per order by an authorized operator/backend process.
10. Order tracking uses internal order/payment states.
11. Duitku POP Sandbox is the only payment gateway environment in V1.
12. Refunds are performed manually outside the application; the outcome is recorded through the backend.
13. No admin dashboard is included in V1.
14. The browser is never authoritative for payable totals, stock, payment success, or final order status.
15. Uncertain payment outcomes must remain recoverable/reviewable rather than being converted into guessed success or failure states.

## 9. Dependencies and External Systems

- Supabase project for PostgreSQL and Edge Functions.
- Duitku merchant Sandbox account and official POP API documentation.
- Frontend hosting/runtime environment, to be selected and documented separately.

Credentials, project identifiers, endpoint contracts, callback URLs, and environment-variable names must be documented only from verified configuration. Never add secret values to this file.

## 10. Open Questions

The following details are not fully specified by this requirements baseline and must be resolved before the relevant implementation:

- **OQ-001 — Checkout fields:** exact required customer, contact, and delivery fields.
- **OQ-002 — Shipping operation:** exact operator workflow and when the shipping amount becomes final/visible to the customer.
- **OQ-003 — Guest order access:** secure customer-facing method for retrieving an order without customer authentication.
- **OQ-004 — Payment recovery:** exact customer-facing behavior and operator escalation path for ambiguous provider outcomes.
- **OQ-005 — Operator authorization:** concrete identity/authorization mechanism for operator-only backend actions.
- **OQ-006 — Catalogue presentation:** product attributes and filtering/sorting behavior beyond simple categories.
- **OQ-007 — UX details:** exact responsive breakpoints, accessibility target, and detailed page-level interaction behavior.

Open questions do not authorize an AI agent to invent business rules. Resolve them through explicit product decisions before implementing the affected behavior.

## 11. Acceptance and Traceability

- Each requirement has a stable ID for discussion, implementation, and verification.
- Acceptance criteria will be maintained separately in `ACCEPTANCE_CRITERIA.md`.
- Approved business and architecture decisions will be maintained in `DECISIONS.md`.
- The target architecture will be documented in `ARCHITECTURE.md`.
- Implementation sequencing and progress will be maintained in `IMPLEMENTATION_PLAN.md`.
- Update this document when a requirement is approved, changed, superseded, or removed. Preserve the decision history in `DECISIONS.md`.

## 12. Change Control

- This document remains **DRAFT** until the user explicitly approves it as the V1 requirements baseline.
- An AI agent may identify inconsistencies and propose edits, but must not silently change approved business rules.
- Any change to scope, inventory lifecycle, order/payment lifecycle, shipping, refunds, authorization, or security boundaries requires an explicit user decision.
- Documentation does not prove implementation. Verify actual source code, migrations, functions, and tests before reporting a requirement as implemented.
