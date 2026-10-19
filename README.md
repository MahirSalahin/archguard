# 🛡️ ArchGuard

> **Universal Software Architecture Guardrails & Senior Engineer Guidelines for AI Coding Agents**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Format: Agent Skill](https://img.shields.io/badge/Format-Agent%20Skill%20%2F%20Rule-8A2BE2.svg)](SKILL.md)
[![Ecosystem](https://img.shields.io/badge/Compatible%20with-Antigravity%20%7C%20Claude%20Code%20%7C%20Cursor%20%7C%20Windsurf-success.svg)](#installation--setup)

**ArchGuard** is an open-source architectural guardrail and pair-programming skill for AI coding assistants (Google Antigravity, Claude Code, Cursor, Windsurf, Copilot, etc.).

AI coding assistants are fast, but left unchecked they frequently introduce architectural decay:
- Dumping database queries and validation schemas directly inside HTTP route handlers.
- Leaking framework transport objects (`Request`, `Response`, `HttpContext`) deep into business logic.
- Silently swallowing exceptions or hardcoding tenant-specific values.
- Performing massive, unnecessary code rewrites that break unrelated tests.

**ArchGuard transforms your AI assistant from a junior copy-paster into a disciplined Senior Software Architect.**

---

## 🌟 Key Capabilities

| Capability | What ArchGuard Enforces |
|---|---|
| **🧱 Strict Layer Isolation** | Clear boundaries between Transport, Schemas/DTOs, Services, and Data Access. |
| **⚠️ Pre-Execution Warnings** | If you ask the agent for a quick hack or anti-pattern, it flags the risks and proposes the clean solution before touching code. |
| **🏷️ White-Label by Default** | Zero hardcoded client names, URLs, or business constants in domain code. Everything is config-driven. |
| **🧪 Testing & Regression Gate** | Verification-first discipline: runs project tests before finishing and ensures 100% pass rates. |
| **✂️ Surgical Minimal Diffs** | Restricts AI edits strictly to your requested task without reformatting untouched code or breaking comments. |
| **📝 Conventional Commits** | Automatically formats commit messages in standard Conventional Commit syntax. |

---

## 🏛️ Layer Isolation Matrix

ArchGuard enforces single responsibility across every application tier regardless of your language (Python, TypeScript, Go, Java, Rust, C#):

```text
┌────────────────────────────────────────────────────────┐
│               Routes / Controllers / API               │ ◄── HTTP / Transport & Serialization
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│                     Schemas / DTOs                     │ ◄── Contracts, Validation & Serialization
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│                 Services / Use-Cases                   │ ◄── Domain Rules & Orchestration
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│             Repository / DB / Data Access              │ ◄── Persistence & Queries
└────────────────────────────────────────────────────────┘
```

| Layer | Responsibility | What Belongs Here | What is FORBIDDEN Here |
|---|---|---|---|
| **Routes / Controllers** | Transport & Serialization | Path routing, parameter parsing, HTTP status codes, service calls. | Inline schemas, direct DB/ORM queries, business domain logic. |
| **Schemas / DTOs** | Data Transfer & Validation | Request/Response validation, serialization models, type contracts (e.g., Pydantic, Zod, Protobuf). | Database queries, business logic, ORM relationships. |
| **Services / Use-Cases** | Domain Logic & Orchestration | Business rules, calculations, AI orchestration, state machine transitions. | Direct transport objects (`Response`, `res.status()`), raw SQL/connections. |
| **Repository / DB** | Persistence & Query Execution | Database queries, transactions, ORM/query builder calls (e.g., SQLAlchemy, Prisma, Hibernate, SQLx). | Transport objects, AI provider calling, domain business logic. |
| **Prompts / Assets** | AI Instructions & Copy Assets | Markdown/text prompt templates with placeholders, i18n copy strings. | Executable application code, hardcoded DB queries. |
| **Core / Config** | Technical Environment Setup | Settings classes, environment parsing, timeouts, thread pools, loggers. | Business logic, transient request state. |

---

## ⚠️ Anti-Pattern Warning Mandate

When an AI agent is instructed to take an architectural shortcut, ArchGuard intercepts the command with a structured warning:

```markdown
> ⚠️ Architectural Note / Anti-Pattern Warning:
> - The Risk: [Explains tech debt, testing difficulty, or layer coupling]
> - Industry Standard: [Explains the clean, conventional alternative]
> - Recommendation: [Presents the recommended pattern vs. quick workaround]
```

👉 **[See a complete demonstration of Anti-Pattern Warnings in action](./examples/anti-pattern-warning-demo.md)**

---

## 🚀 Installation & Setup

### 1. Google Antigravity (AGY)

#### Option A: Workspace / Project-Level (Shared with your team)
Place ArchGuard in your project's `.agents/skills/` directory:
```bash
mkdir -p .agents/skills/archguard
curl -o .agents/skills/archguard/SKILL.md https://raw.githubusercontent.com/MahirSalahin/archguard/main/SKILL.md
```

#### Option B: Global (Available across all local projects)
Clone ArchGuard directly into your global Antigravity config:
```bash
git clone https://github.com/MahirSalahin/archguard.git ~/.gemini/config/skills/archguard
```

#### Option C: Always-On Project Rule
If you prefer ArchGuard to be active on **every single prompt** rather than on-demand:
```bash
mkdir -p .agents/rules
cp SKILL.md .agents/rules/archguard.md
```

---

### 2. Claude Code
Add to your project's `.claude/skills/` or prompt instructions:
```bash
mkdir -p .claude/skills/archguard
curl -o .claude/skills/archguard/SKILL.md https://raw.githubusercontent.com/MahirSalahin/archguard/main/SKILL.md
```

---

### 3. Cursor & Windsurf
Add ArchGuard to `.cursorrules` or `.windsurfrules` at your repository root:
```bash
cat SKILL.md >> .cursorrules
```

---

## 🎯 How to Use

Once installed, ArchGuard operates autonomously. You can also explicitly trigger it:

- **Designing New Features**:
  > *"Design a user registration flow following archguard guidelines."*
- **Code Reviews**:
  > *"Review this pull request against archguard anti-patterns."*
- **Refactoring**:
  > *"Refactor this controller to isolate transport and service logic per archguard standards."*

---

## 📄 Conventional Commit Suggestions

At the conclusion of code modifications, ArchGuard produces structured git commit suggestions:

```git
feat(billing): extract invoice calculation into domain service

- decouple payment gateway transport logic from checkout handler
- add unit tests for discount edge cases
```

---

## 🤝 Contributing

Contributions, feedback, and additions are welcome!
1. Fork the repository
2. Create your feature branch (`git checkout -b feature/framework-preset`)
3. Commit your changes (`git commit -m 'feat: add NestJS architecture reference'`)
4. Push to the branch (`git push origin feature/framework-preset`)
5. Open a Pull Request

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).
