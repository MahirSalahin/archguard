---
name: archguard
description: >
  Enforces clean software architecture, DRY, KISS, YAGNI, layer separation,
  anti-pattern detection, and conventional git commit formatting. Use when
  designing modules, refactoring, performing code reviews, creating new features,
  or invokes /archguard.
---

# ArchGuard: Architecture Standards & Guardrails

Enforces clean architecture, strict layer isolation, anti-pattern detection, and sustainable engineering standards across all languages and frameworks.

---

## 1. Core Engineering Principles

- **KISS**: Choose the simplest complete solution. Avoid unnecessary design patterns when simple functions or standard classes suffice.
- **DRY**: Every piece of domain logic or schema must have a single authoritative representation. Extract reusable utilities.
- **YAGNI**: Build strictly for current requirements. Reject speculative abstractions or premature infrastructure.
- **Root-Cause Resolution (No Band-Aid Patches)**: Fix root causes, never superficial symptoms. Never use quick monkey-patches, hardcoded bypasses, or suppression comments (`# type: ignore`, `@ts-ignore`, `eslint-disable`) that accumulate technical debt.

---

## 2. Layer Isolation Matrix

Every module must adhere to a single responsibility:

| Layer | Responsibility | Allowed | Forbidden |
|---|---|---|---|
| **Routes / API** | Transport & Serialization | Routing, param parsing, status codes, service delegation. | Inline schemas, direct DB/ORM queries, business domain logic. |
| **Schemas / DTOs** | Contracts & Validation | Request/Response DTOs, type validation (e.g. Pydantic, Zod, TS interfaces, Protobuf). | Database queries, business logic, ORM relations. |
| **Services** | Domain Logic & Orchestration | Business rules, calculations, AI orchestration, state flows. | Direct transport objects (`Response`, `JSONResponse`, `res.status()`), raw DB access. |
| **Repository / DB** | Persistence & Data Access | DB queries, transactions, ORM calls (e.g. SQLAlchemy, Prisma, TypeORM, Hibernate, SQLx). | Transport objects, AI client calling, domain rules. |
| **Prompts / Copy** | AI Instructions & Assets | Template strings with placeholders, i18n copy assets. | Executable application code, database queries. |
| **Core / Config** | Environment & Technical Setup | Config classes, env parsing (e.g. Pydantic Settings, dotenv, viper), timeouts, pools, loggers. | Business logic, transient request state. |

---

## 3. Senior Engineer Anti-Pattern Warning Mandate

Warn the user before implementing an anti-pattern, violating layer boundaries, or introducing technical debt:

> ⚠️ **Architectural Warning:**
> - **Risk**: [Explain why this approach creates tech debt, breaks testing, or couples layers]
> - **Clean Alternative**: [Explain the industry standard pattern]
> - **Recommendation**: [Ask whether to proceed with clean pattern or quick workaround]

### Anti-Pattern Checklist:
1. **Inline Schemas**: DTOs/models defined inside route or service files instead of schema modules.
2. **Hardcoded Prompts/Copy**: Multi-line LLM prompts or marketing copy inside code string literals.
3. **Transport Leakage**: Passing framework objects (`Request`, `Response`, `HttpContext`) into services/repositories.
4. **God Functions/Classes**: Single module handling routing, validation, DB queries, and AI logic all at once.
5. **Premature Abstractions**: Speculative generalization or complex multi-tenancy before basic flows work.
6. **Silent Error Swallowing**: Catching broad exceptions without logging, tracing, or fallback results.
7. **Hardcoded Domain Values**: Hardcoding client names, product IDs, tenant IDs, or fixed URLs in code.
8. **Band-Aid Patches**: Superficial try/catches, suppression comments, or hacky bypasses hiding design flaws.

---

## 4. Operational Guardrails

- **White-Label by Default**: Zero hardcoded client/brand names or vendor URLs. Load all app names, feature flags, limits, and parameters from environment settings or database configs.
- **Verification Gate**: Execute project test runners (`pytest`, `npm test`, `cargo test`, `go test`) before finishing. Maintain 100% test pass rate. Add tests for newly created endpoints or services.
- **Living Documentation**: Update `README.md`, docstrings, `.env.example`, and architecture notes whenever code changes affect them. Zero documentation drift.
- **Scope Discipline**: Make surgical minimal diffs strictly addressing the request. Never reformat untouched code. Preserve existing comments and docstrings.

---

## 5. Git Conventional Commit Format

Conclude all code modification responses with a Conventional Commit suggestion:

```git
<type>(<scope>): <concise description in imperative mood>

- <key bullet 1>
- <key bullet 2>
```
Types: `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `chore`.
---