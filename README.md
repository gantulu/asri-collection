# Ecommerce Apps

Modern, responsive e-commerce storefront built with React, Vite, and TypeScript. The V1 scope focuses on guest shopping, order creation, and Duitku POP Sandbox payment processing, backed by Supabase.

> **Project status:** Documentation baseline / V1 specification. The repository is currently an empty project shell; implementation details, exact database objects, API contracts, and environment variable names must be verified as they are added.

## 1. Project Overview

Ecommerce Apps is a mobile-first storefront application for browsing products, managing a shopping cart, completing guest checkout, and viewing order/payment status.

The application uses:
- **Frontend:** React, Vite, TypeScript.
- **Backend:** Supabase Database and Edge Functions.
- **Payment:** Duitku POP Sandbox.
- **Currency:** Indonesian Rupiah (IDR).
- **Checkout model:** Guest checkout; no customer login in V1.

## 2. Project Goals

- Deliver a modern, responsive storefront for mobile and desktop.
- Provide product discovery, product details, and cart management.
- Support guest checkout without requiring customer accounts.
- Validate inventory and create orders through trusted backend operations.
- Integrate Duitku POP Sandbox for payment initiation and callback processing.
- Provide internal order and payment status visibility.
- Keep V1 scope focused and avoid introducing an admin dashboard or unnecessary production features prematurely.

## 3. Technology Stack

| Layer | Technology | Responsibility |
| --- | --- | --- |
| Frontend | React | UI and component rendering |
| Build tooling | Vite | Development server and production build |
| Language | TypeScript | Static typing |
| Backend platform | Supabase | Backend platform |
| Database | Supabase PostgreSQL | Products, inventory, orders, and payment records |
| Server-side logic | Supabase Edge Functions | Trusted checkout, order, and payment operations |
| Payment gateway | Duitku POP Sandbox | Payment initiation and payment status callbacks |
| Client routing | React Router | Application navigation |
| Client state | Zustand | Local/client-side state |
| Server state | TanStack Query | Remote data fetching and cache lifecycle |
| Validation | Zod | Runtime input and payload validation |

The stack above is the approved target stack. Exact package versions and installed dependencies must be confirmed from the repository manifest when implementation is added.

## 4. Application Architecture

The application follows a **feature-based architecture**:
- Feature modules own domain-specific UI and behavior.
- Shared UI components remain separate from business logic.
- API and service modules isolate backend communication.
- Zustand manages client-side state, such as cart interaction.
- TanStack Query manages server state and asynchronous data.
- Zod validates untrusted inputs and API payloads.
- React Router defines route composition.
- Sensitive operations are executed by Supabase Edge Functions, not directly by the browser.

### High-level request flow

1. The storefront requests public product data.
2. The customer selects products and manages the cart in the client.
3. Checkout submits the required order data to a backend function.
4. The backend validates the request and stock, then creates the order and payment workflow.
5. The backend initiates Duitku POP payment creation.
6. Duitku sends payment updates to the backend callback endpoint.
7. The storefront reads order/payment status through the supported backend service.

The browser must not be trusted to determine payable totals, inventory availability, payment success, or final order status.

## 5. Project Structure

The following is the intended structure, not a claim about files already present in the repository:

```text
app/
├── public/
├── src/
│   ├── app/
│   │   ├── router/
│   │   └── providers/
│   ├── assets/
│   ├── components/
│   │   ├── ui/
│   │   └── shared/
│   ├── features/
│   │   ├── home/
│   │   ├── products/
│   │   ├── categories/
│   │   ├── cart/
│   │   ├── checkout/
│   │   ├── orders/
│   │   └── payments/
│   ├── layouts/
│   ├── hooks/
│   ├── lib/
│   ├── services/
│   ├── stores/
│   ├── types/
│   └── main.tsx
├── .env.example
├── package.json
├── tsconfig.json
└── vite.config.ts
```

Keep feature-specific components and logic inside their feature module. Promote code to shared modules only when it is genuinely reused.

## 6. Features & Modules

### Storefront
- Home page and product discovery.
- Product listing and simple category filtering.
- Product detail page.
- Responsive navigation and storefront layout.

### Product catalogue
- Product data is managed in Supabase Database.
- Simple product categories.
- Product variants support size and color.
- Variant pricing is the same as the parent product price.

### Cart
- Add, remove, and update cart items.
- Display item quantities and calculated subtotal.
- Revalidate product availability and prices on the backend during checkout.

### Checkout and orders
- Guest checkout only; no customer login.
- Capture the information required to fulfil an order.
- Shipping cost is entered manually by the operator/backend per order.
- Order tracking is internal and based on order status.
- Store transaction snapshots so historical orders do not depend on mutable product data.

### Payments
- Duitku POP Sandbox for V1.
- Backend-controlled payment creation and callback handling.
- Payment recovery and duplicate-request protection.
- Refunds are handled manually outside the application; the outcome is recorded through the backend.

### Explicit V1 exclusions
- Customer authentication and account management.
- Admin dashboard.
- Automated shipping-rate calculation or carrier integration.
- Automated refund execution.
- Production Duitku credentials or live payment processing.

## 7. Pages & Routing

Pages are grouped by navigation context:

```text
pages/
├── main/
│   ├── home.tsx
│   ├── products.tsx
│   ├── product-detail.tsx
│   └── cart.tsx
└── standalone/
    ├── checkout.tsx
    ├── order-status.tsx
    └── payment-result.tsx
```

| Page | Purpose | Context |
| --- | --- | --- |
| Home | Storefront landing page and product discovery | Main |
| Products | Product catalogue and category filtering | Main |
| Product Detail | Product information, variant selection, and add-to-cart | Main |
| Cart | Review and update cart items | Main |
| Checkout | Submit guest order and initiate payment | Standalone |
| Order Status | View internal order and payment status | Standalone |
| Payment Result | Present the result/return state after payment navigation | Standalone |

The route paths are not specified here because the actual router configuration has not yet been committed. Do not assume a URL path from a page filename.

## 8. UX Behavior Specification

- Use a mobile-first responsive layout with appropriate desktop adaptations.
- Keep the storefront navigation consistent across main pages.
- Keep checkout, order status, and payment result focused on their task rather than the storefront navigation.
- Provide clear loading, empty, validation-error, success, and failure states.
- Keep cart changes responsive and show updated quantities and totals.
- Show product variant selection before adding a variant-based item to the cart.
- Prevent checkout submission when required fields are invalid or the cart is empty.
- Revalidate prices and stock on the backend; client calculations are display-only.
- Prevent accidental duplicate checkout submissions.
- Clearly communicate that order creation and payment completion are separate states.
- Do not treat the browser return page as authoritative proof of payment. Payment status must come from the backend-verified payment state.
- Provide a recoverable order-status path when a payment is pending, delayed, failed, or expired.

## 9. Database Architecture

Supabase PostgreSQL is the source of truth for catalogue, inventory, orders, and payment records.

The V1 database design must preserve these rules:

- Product management is database-backed; categories are simple.
- Product variants support size and color and inherit the parent product price.
- Validate available stock during checkout.
- Decrease stock when the order is created.
- Restore stock when payment is confirmed as failed or expired, with duplicate-safe inventory handling.
- Store order and order-item snapshots for historical accuracy.
- Use explicit order and payment lifecycle states with database constraints.
- Make checkout idempotent using an idempotency key and request/payload hash.
- Handle callbacks that arrive while payment creation is still in progress through a deferred/recoverable flow.
- Record inventory movements with explicit movement types.
- Define foreign-key deletion behavior deliberately.
- Retain only necessary provider response data and sanitize sensitive/unneeded fields.
- Keep refund execution manual and record its outcome through a trusted backend operation.
- Store any refund evidence in a private Supabase Storage bucket; do not expose private evidence paths as public URLs.

Exact table names, columns, SQL migrations, constraints, and RPC signatures must be documented from the actual migration files when they are created. This README does not define an independent schema contract.

## 10. API & Services

The frontend communicates with backend capabilities through a dedicated service layer.

Expected service responsibilities:
- Product and category retrieval.
- Checkout/order creation.
- Order status retrieval.
- Payment creation and payment status retrieval where exposed by the backend.

Supabase Edge Functions own privileged workflows, including:
- Server-side checkout validation.
- Order creation and inventory changes.
- Duitku payment request signing and submission.
- Duitku callback signature and payload validation.
- Payment state transitions and recovery.
- Recording manual refund outcomes.

Exact function names, HTTP methods, request/response schemas, and endpoint URLs must be taken from deployed function definitions or committed API contracts. They are intentionally not invented in this document.

## 11. Payment Integration (Duitku POP)

**V1 environment: Duitku POP Sandbox only.**

Official documentation: [Duitku POP Documentation](https://docs.duitku.com/pop/id/)

Integration principles:
- Create payment requests from a trusted backend function.
- Keep merchant credentials and signing secrets on the server; never expose them in frontend bundles.
- Calculate and validate the payable amount on the backend.
- Validate Duitku callback authenticity using the official signature procedure.
- Validate callback fields and transaction amount before applying state changes.
- Handle duplicate callbacks idempotently.
- Do not mark an order paid based only on a browser redirect or query parameter.
- Preserve a recoverable state when payment creation or callback processing is ambiguous.
- Restore reserved/decreased stock only when the backend establishes a failed or expired payment outcome.
- Keep refunds manual and record their outcomes in the backend.

The official Duitku documentation is the source of truth for request parameters, signature formulas, callback formats, and provider-specific response semantics. Do not infer undocumented fields or result-code behavior.

## 12. Environment Configuration

Use environment variables for project configuration. Commit only placeholder names and descriptions in `.env.example`; never commit actual credentials.

Configuration categories may include:
- Supabase project URL and the appropriate public client key.
- Duitku Sandbox merchant configuration and server-side credentials.
- Application public base URL and backend callback/return URL configuration.

The exact variable names must match the implementation and deployment configuration. Do not put Supabase service-role credentials, Duitku private keys, or signing secrets in variables exposed to the Vite client (for example, variables prefixed with `VITE_`).

## 13. Installation & Setup

Prerequisites:
- Node.js version supported by the project dependencies.
- npm (or the package manager selected by the committed lockfile).
- Access to the Supabase project used for development.
- Duitku Sandbox merchant credentials for payment integration testing.

Once the application manifest is added:

```bash
git clone https://github.com/gantulu/asri-collection.git
cd asri-collection
npm install
```

Create a local environment file based on the committed template:

```bash
cp .env.example .env.local
```

Fill in the required values locally. Do not commit `.env.local`.

The following commands are conventional Vite scripts; confirm their exact availability in `package.json`:

```bash
npm run dev
npm run build
npm run preview
```

Supabase migrations and Edge Functions must be tested in the intended development environment before being applied or deployed to a shared/production environment.

## 14. Development Workflow

Follow the project workflow:

**Audit → Analyze → Recommend → Requirement → Implement → Verify → Iterate**

1. Inspect the current repository and existing implementation before proposing changes.
2. Confirm requirements and record decisions before implementation.
3. Keep changes scoped to the approved requirement/version.
4. Use official provider documentation for Supabase and Duitku contracts.
5. Keep database migrations, Edge Functions, frontend services, and API contracts aligned.
6. Test business-critical flows, including duplicate checkout requests, stock changes, duplicate callbacks, and payment recovery.
7. Verify type checks, linting, builds, and relevant tests when the scripts and test suites exist.
8. Update this README when approved architecture or implementation behavior changes.

Do not treat a proposal or roadmap item as implemented functionality.

## 15. Security & Validation

- Never expose service-role keys, Duitku merchant secrets, or signing keys in client-side code.
- Keep privileged database writes and payment operations inside Edge Functions.
- Validate and normalize all client-supplied checkout fields on the backend.
- Recalculate order totals and verify stock on the backend.
- Use idempotency controls for order creation and callback processing.
- Verify Duitku callback signatures and transaction values before updating payment/order state.
- Apply explicit authorization to operator-only backend actions.
- Do not trust browser redirects as payment confirmation.
- Keep private refund evidence in private storage.
- Avoid logging credentials, access tokens, or unnecessary personal/payment data.
- Apply least-privilege access to database tables, storage, and Edge Functions.

## 16. Project Requirements

| ID | Requirement | V1 decision |
| --- | --- | --- |
| PR-01 | Frontend framework | React + Vite + TypeScript |
| PR-02 | Backend | Supabase Database + Edge Functions |
| PR-03 | Checkout | Guest checkout only |
| PR-04 | Currency | IDR only |
| PR-05 | Product management | Supabase Database |
| PR-06 | Categories | Simple categories |
| PR-07 | Product variants | Size and color |
| PR-08 | Variant pricing | Same as parent product |
| PR-09 | Inventory | Validate at checkout; decrease on order creation |
| PR-10 | Failed/expired payment | Restore stock through a duplicate-safe backend flow |
| PR-11 | Shipping | Manual cost set per order by operator/backend |
| PR-12 | Order tracking | Internal status lifecycle |
| PR-13 | Payment gateway | Duitku POP Sandbox |
| PR-14 | Refund | Manual outside system; outcome recorded through backend |
| PR-15 | Admin dashboard | Excluded from V1 |
| PR-16 | Operator actions | Backend authorization required |

## 17. Development Roadmap

Roadmap items are planned work, not claims of completion.

1. **Foundation**
   - Establish Vite + React + TypeScript application.
   - Add routing, providers, shared UI, and feature module conventions.
   - Add environment template and development scripts.

2. **Storefront**
   - Implement home, product listing, product detail, and cart.
   - Connect product/category reads to Supabase.
   - Add responsive and accessible UI states.

3. **Database & backend**
   - Finalize and test schema migrations.
   - Implement trusted checkout and order creation.
   - Implement stock movement and payment recovery rules.
   - Add order-status service.

4. **Duitku POP Sandbox**
   - Implement backend payment creation.
   - Implement callback verification and idempotent processing.
   - Test pending, successful, failed, expired, duplicate, and ambiguous outcomes.

5. **Guest checkout experience**
   - Implement checkout, payment result, and order status pages.
   - Verify shipping cost handling and order snapshots.
   - Test end-to-end flows on mobile and desktop.

6. **Verification & release readiness**
   - Run type checks, linting, tests, and production build.
   - Review security boundaries, environment configuration, and operational failure handling.
   - Document remaining gaps before any production-scope decision.


## 18. AI Instructions & Automated Repository Maintenance

AI coding agents working on this repository must read and follow [`ai-instruction.md`](./ai-instruction.md) before auditing, planning, modifying, testing, or integrating changes.

### Navigation

- **[AI Instructions — ai-instruction.md](./ai-instruction.md):** complete operating rules for AI agents.
- **[Project README — README.md](./README.md):** project overview, architecture, requirements, and development documentation.

### How to use

1. Open and read `ai-instruction.md` at the beginning of an AI-assisted repository task.
2. Audit the latest repository state, current branch, project requirements, and relevant official documentation.
3. Follow the defined **Audit → Analyze → Plan → Implement → Verify → Report** workflow.
4. Automatically fix clear, low-risk issues that are within the instruction's allowed scope.
5. Stop and request an explicit decision for product changes, destructive database operations, security-boundary changes, payment lifecycle changes, deployment, secrets, or other restricted actions.
6. Run the relevant checks and report actual results; never claim a check passed if it was not executed.
7. Integrate changes into `main` only when the repository's branch rules and required checks permit it. Use a pull request when review or CI is required; never bypass branch protection or required reviews.

### Automation boundary

The instruction file defines the AI agent's operating policy; it does not itself install or trigger a scheduled automation. Automatic execution requires an AI agent or repository workflow that is configured to read and follow the file. Merge to `main` is conditional on repository permissions, branch protection, required reviews, and successful verification.


---

## Documentation & References

- [Duitku POP Documentation (Indonesia)](https://docs.duitku.com/pop/id/)
- [Supabase Documentation](https://supabase.com/docs)
- [React Documentation](https://react.dev/)
- [Vite Documentation](https://vite.dev/guide/)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
