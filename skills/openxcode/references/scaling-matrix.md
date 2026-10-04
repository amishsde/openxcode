# Architecture Scaling Matrix (Small Script to Enterprise Monorepo)

This matrix defines the strict structural standards OpenXCode enforces based on project tier. Never over-engineer small tools, and never under-architect enterprise codebases.

---

## 1. Project Scale Tiers

### Tier 1: Micro Utility / Script / CLI (< 500 LOC)
* **Goal**: Extreme minimalism, fast execution, zero boilerplate.
* **Layout**:
  ```text
  ├── src/ or ./
  │   ├── main.ext (or index.ext)
  │   └── utils.ext (pure helpers only if > 150 LOC)
  ├── package.json / requirements.txt / Cargo.toml
  └── README.md
  ```
* **Rules**:
  - Single-file execution is preferred if under 200 lines.
  - Standard library only. 0 non-essential runtime dependencies.
  - Direct parameter/CLI flag parsing without heavy command framework bloat unless required.

---

### Tier 2: Mid-Scale Application / Modular Service (500 – 10,000 LOC)
* **Goal**: Predictable Separation of Concerns, high testability, zero regression.
* **Layout**:
  ```text
  ├── src/
  │   ├── components/       # Presentational UI (atoms/molecules, < 250 LOC per file)
  │   ├── pages/ or app/    # Route-level views and screen containers
  │   ├── services/ or api/ # Network, database queries, and external APIs
  │   ├── hooks/ or state/  # Reactive state and business lifecycle hooks
  │   ├── types/            # TypeScript interfaces, contracts, and schema validators
  │   └── utils/            # Pure helpers and formatters (deterministic)
  ├── tests/                # Unit and integration test suites
  ├── .env.example          # Externalized environment template (no credentials)
  └── package.json
  ```
* **Rules**:
  - Anti-Monolith strictly enforced: max 250–300 lines per file.
  - Strict UI and business logic separation: UI components must never make raw database calls.
  - Pure functions in `utils/` must be 100% side-effect free.

---

### Tier 3: Enterprise Multi-Domain / Microservices / Monorepo (> 10,000 LOC)
* **Goal**: Domain-Driven Design (DDD), bounded contexts, multi-package resilience, high concurrency.
* **Layout (Workspace / Monorepo or Modular Domain)**:
  ```text
  ├── apps/
  │   ├── web/              # Customer web application
  │   ├── admin/            # Internal operations portal
  │   └── api/              # Core backend / BFF API gateway
  ├── packages/ or modules/ # Shared domain libraries
  │   ├── ui-system/        # Canonical Design System tokens and primitives
  │   ├── config/           # Shared TypeScript, ESLint, and security configs
  │   ├── auth/             # Centralized IAM, token validation, RBAC
  │   ├── db/               # Prisma / Drizzle / SQL migrations and schemas
  │   └── logger/           # Structured telemetry and safe redacting logger
  ├── turbo.json / nx.json  # Workspace build pipelines
  ├── .env.example
  └── docker-compose.yml
  ```
* **Rules**:
  - Strict circular dependency prohibition (`madge` or language equivalents).
  - Explicit boundaries: Cross-package imports must use published workspace package paths (`@company/ui-system`), never deep relative hacks (`../../packages/ui/src/...`).
  - Independent versioning and isolated environment configurations.
  - Centralized error boundaries, structured error logging (with PII redaction), and distributed tracing context (`trace-id`).

---

## 2. Dynamic Scale Detection Algorithm

When analyzing an active codebase, OpenXCode automatically infers the project tier:
1. **Detect Workspace Manifest**: Look for `workspaces` in `package.json`, `pnpm-workspace.yaml`, `Cargo.toml` (`[workspace]`), or `go.work`. If present -> **Enforce Tier 3**.
2. **Detect Directory Depth & LOC**:
   - If total source files <= 5 and LOC < 500 -> **Apply Tier 1**.
   - If total source files > 5 or routes/components exist -> **Apply Tier 2**.
3. **Refactor Guard**: Never upgrade a Tier 1 script into a multi-folder monster without explicit user intent. Always match the appropriate tier.
