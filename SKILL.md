---
name: archguard
description: >
  Enforces clean software architecture, DRY, KISS, YAGNI, layer separation,
  anti-pattern detection, and conventional git commit formatting. Use when
  designing modules, refactoring, performing code reviews, creating new features,
  or invokes /archguard.
---

# Software Architecture Standards & Best Practices

This skill provides comprehensive architectural guidelines, layer isolation rules, anti-pattern detection checklists, and commit formatting standards for professional software development across all programming languages and frameworks.

---

## 1. Core Engineering Principles

### KISS (Keep It Simple, Stupid)
- Choose the simplest solution that completely solves the problem.
- Avoid unnecessary design patterns (e.g. abstract factory, complex registries) when a simple function or standard class suffices.
- Code should be immediately understandable to any engineer reading it for the first time.

### DRY (Don't Repeat Yourself)
- Every piece of domain knowledge or business logic must have a single, unambiguous representation in the system.
- Extract common validation, formatting, and database queries into reusable utilities or service functions.
- Avoid duplicate schemas: do not redefine the same data transfer object or schema in multiple places.

### YAGNI (You Aren't Gonna Need It)
- Implement only what is needed for the current requirements.
- Do not build speculative abstractions for hypothetical future needs (e.g., do not build full multi-tenant distributed infrastructure before single-tenant logic is clean and working).
- Add complexity only when proven necessary by evolving requirements.

### Separation of Concerns (SoC) & Layer Isolation
Every module must have a single, well-defined responsibility:

| Layer | Responsibility | What Belongs Here | What is FORBIDDEN Here |
|---|---|---|---|
| **Routes / Controllers / API** | Transport & Serialization | Path routing, parameter parsing, HTTP/gRPC status codes, calling services. | Inline DTO schemas, direct DB/ORM queries, business domain logic. |
| **Schemas / DTOs** | Data Transfer & Validation | Request/Response validation, serialization models, type contracts (e.g., Pydantic, Zod, Protobuf, TS interfaces). | Database queries, business logic, ORM entity relationships. |
| **Services / Use-Cases** | Domain Logic & Orchestration | Business rules, calculations, AI orchestration, state machine transitions. | Direct transport objects (`Response`, `JSONResponse`, `res.status()`), raw database connections. |
| **Repository / DB / Data Access** | Persistence & Query Execution | Database queries, transactions, ORM/query builder calls (e.g., SQLAlchemy, Prisma, TypeORM, Hibernate, SQLx). | Transport objects, AI provider calling, domain business logic. |
| **Prompts / Templates / Copy** | AI Instructions & Copy Assets | Markdown/text prompt templates with placeholders, i18n copy strings. | Executable application code, hardcoded DB queries. |
| **Core / Config** | Environment & Technical Setup | Settings classes, environment variable parsing, timeouts, thread pools, global loggers. | Business logic, transient request state. |

---

## 2. Senior Engineer Anti-Pattern Warning Mandate

Whenever the user requests an approach that is an anti-pattern, violates layer boundaries, or introduces technical debt, **warn the user before implementing it**.

### Anti-Pattern Detection Checklist:
1. **Inline Schemas / Models**: Defining validation schemas or DTO models inside route or service files instead of dedicated schema/contract modules.
2. **Hardcoded Prompts / Business Copy**: Putting multi-line LLM prompts or customer-facing copy directly inside inline code string literals instead of dedicated prompt/template assets.
3. **Transport Layer Leakage**: Passing framework transport objects (e.g., `Request`, `Response`, `HttpContext`, `WebSocket`) deep into domain services or repository layers.
4. **God Functions / Classes**: A single function or file trying to handle validation, routing, DB queries, and AI generation all at once.
5. **Premature Multi-Tenancy / Speculative Generalization**: Designing complex distributed abstractions before basic single-tenant or straightforward workflows are solid.
6. **Silent Error Swallowing**: Catching broad catch-all exceptions/errors without logging, tracing, or returning structured fallbacks.
7. **Hardcoded Domain Values**: Hardcoding specific client business names, product IDs, tenant identifiers, or regional formats directly in core application code.

### Warning Format:
When flagging an anti-pattern, follow this constructive structure:

> ⚠️ **Architectural Note / Anti-Pattern Warning:**
> - **The Risk**: [Explain why this approach creates tech debt, breaks testing, or couples layers]
> - **Industry Standard**: [Explain the clean, conventional alternative]
> - **Recommendation**: [Ask whether to proceed with the recommended clean pattern or the quick workaround]

---

## 3. White-Label & Portability by Default

- **Zero Hardcoded Brand Names**: Core application code must never contain specific client names (e.g. "Acme Corp"), company logos, or hardcoded vendor URLs.
- **Config-Driven Attributes**: Application names, regional policies, feature flags, tax/delivery parameters, currencies, and limits must always be read from `Settings`, environment variables, or database configurations.
- **Dynamic Entity Handling**: Never hardcode category lists, entity IDs, or tenant-specific identifiers directly in code; inspect and handle entities dynamically from loaded catalogs or configuration stores.

---

## 4. Verification & Testing Gate

- **Execute Tests First**: Before declaring any feature or refactoring complete, execute the project test runner (e.g. `pytest`, `npm test`, `cargo test`, `go test`, `mvn test`).
- **Zero Regression**: Preserve a 100% test pass rate. If existing tests fail due to deliberate architectural improvements, update the test assertions to reflect the new architecture.
- **Test What You Change**: Add unit or integration tests for newly created services, prompt loaders, or route endpoints.

---

## 5. Scope Discipline & Minimal Diff

- Make surgical changes strictly addressing the user's intent.
- Do not reformat untouched code or introduce sweeping refactors without the user's explicit consent.
- Preserve existing comments and docstrings.

---

## 6. Git Conventional Commit Format

Conclude all code modification responses with a Conventional Commit suggestion:

```git
<type>(<scope>): <concise description in imperative mood>

- <key bullet 1>
- <key bullet 2>
```

### Supported Types:
- `feat`: New feature or user-facing capability.
- `fix`: Bug fix.
- `refactor`: Code change that neither fixes a bug nor adds a feature.
- `perf`: Performance improvement.
- `test`: Adding or updating tests.
- `docs`: Documentation changes only.
- `chore`: Build process, dependency updates, or configuration changes.
---