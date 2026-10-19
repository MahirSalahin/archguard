# ArchGuard in Action: Anti-Pattern Warning Demo

This document demonstrates how an AI coding assistant (powered by **ArchGuard**) responds when prompted with a request that violates clean architecture or introduces technical debt.

---

## Scenario: Inline DB Query & Schema in a Route Handler

### User Prompt
> "Hey, quickly add an endpoint `/api/v1/checkout` that calculates cart total, creates an order directly in postgres with SQL, and returns `{ success: true }`."

---

### ArchGuard Agent Response

> ⚠️ **Architectural Note / Anti-Pattern Warning:**
> - **The Risk**: Implementing raw database queries and order creation logic directly inside the route handler couples the transport layer to persistence. It prevents reusability across webhooks or worker jobs, complicates mock testing, and violates Separation of Concerns.
> - **Industry Standard**:
>   1. **Route Handler (`api/v1/checkout`)**: Extracts request payload, parses auth token, and delegates to `OrderService.checkout()`.
>   2. **DTO Schema (`schemas/order`)**: Validates request body and response contract.
>   3. **Service Layer (`services/order`)**: Calculates cart totals, enforces business discounts, and coordinates order status.
>   4. **Repository Layer (`repositories/order`)**: Executes the database insert and transaction commit.
> - **Recommendation**: Shall I implement this following the clean 4-tier layer pattern (recommended), or would you prefer a minimal route-level script for quick prototyping?

---

## Scenario: Hardcoded Client / Tenant Details

### User Prompt
> "Hardcode the store name 'Acme Store' and set the delivery fee to $15 for all orders in the checkout calculation."

---

### ArchGuard Agent Response

> ⚠️ **Architectural Note / Anti-Pattern Warning:**
> - **The Risk**: Hardcoding tenant identifiers ("Acme Store") and business parameters ($15 fee) directly in application logic makes the codebase non-portable and creates breaking changes when onboarding new stores or updating pricing models.
> - **Industry Standard**: Extract operational parameters into environment configuration (`Settings`) or load them dynamically from the merchant/store database entity.
> - **Recommendation**: I can define `delivery_fee` and `store_name` as configuration attributes in your app settings/store entity with $15 as the default value. Shall we proceed with this configurable approach?
