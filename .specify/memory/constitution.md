<!--
Sync Impact Report
- Version change: template → 1.0.0
- Modified principles: placeholder principles → five concrete CRM delivery principles
- Added sections: Technical Constraints; Team Workflow and Quality Gates
- Removed sections: none
- Templates requiring updates: ✅ plan-template.md updated; ✅ tasks-template.md updated;
	✅ spec-template.md reviewed with no change required; ✅ .github/prompts/*.prompt.md reviewed;
	✅ README and AGENTS.md reviewed; no .specify/templates/commands/ directory exists
- Follow-up TODOs: confirm the original ratification date and replace TODO(RATIFICATION_DATE)
-->

# BDR CRM Constitution

## Core Principles

### I. Rep-Focused Simplicity
The product MUST prioritize the daily workflow of an individual B2B sales representative:
capture prospects, understand pipeline status, and record or review daily activity. Features
MUST have a clear user outcome and MUST avoid enterprise CRM complexity unless a requirement
demonstrates its value. This keeps the product fast to learn and maintain.

### II. Type-Safe Next.js Architecture
The application MUST use Next.js App Router with TypeScript in strict mode. Production code
MUST NOT use `any`; unknown external data MUST be validated or narrowed before use. File-based
routing MUST define page structure. Server Components are the default, Client Components MUST
be limited to interactive browser behavior, Server Actions MUST handle mutations from the UI,
and Route Handlers MUST be used for API-facing needs. These boundaries keep rendering,
mutation, and integration responsibilities explicit.

### III. Consistent, Accessible UI
The interface MUST use Tailwind CSS utility classes as the primary styling approach. Custom CSS
MUST be added only when Tailwind cannot express the required behavior or when it defines a
shared, documented primitive. Repeated UI patterns MUST use consistent naming and composition,
and interactive controls MUST expose usable labels, keyboard access, focus states, and clear
loading or error states. This makes a small CRM predictable during repeated daily use.

### IV. Safe, Explicit Data Access
PostgreSQL is the system of record and MUST be accessed through raw SQL using the `pg`
node-postgres library; an ORM MUST NOT be introduced. Database access MUST use a shared
connection pool, parameterized queries, and explicit transaction boundaries where multiple
writes must succeed together. Migrations MUST be versioned, committed, repeatable in a fresh
database, and reviewed with the queries they support. No secret or connection credential may
be committed. These rules protect prospect data and prevent SQL injection and schema drift.

### V. Tested, Reviewable Team Delivery
Every feature MUST include tests appropriate to its risk: unit tests for business rules,
database integration tests for SQL and persistence behavior, and end-to-end coverage for
critical rep workflows. Defects MUST include a regression test when practical. A change is not
complete until linting, type-checking, relevant tests, and a production build pass. Each change
MUST be small enough for another team member to review, describe its acceptance behavior, and
identify its owner; shared work MUST be coordinated to avoid silent contract changes.

## Technical Constraints

The required implementation stack is Next.js App Router, TypeScript, Tailwind CSS, and
PostgreSQL. PostgreSQL access is exclusively through `pg` and raw parameterized SQL, with no
ORM or query builder. TypeScript compiler settings MUST enable strict mode. Environment-specific
configuration MUST be supplied through environment variables and documented without exposing
values. Features MUST preserve server/client boundaries and MUST NOT move secrets or database
calls into browser-delivered code.

## Team Workflow and Quality Gates

The team MUST agree on the feature scope and acceptance scenarios before implementation. Names
MUST be descriptive and consistent: PascalCase for React components and TypeScript types,
camelCase for variables, functions, and Server Actions, lowercase kebab-case for route segments,
and lowercase snake_case for PostgreSQL tables and columns. SQL keywords and migration intent
MUST be easy to identify in migration files.

Each pull request MUST state the user-visible change, relevant database or API contract impact,
validation performed, and any follow-up work. At least one other team member MUST review changes
before integration. The author MUST resolve lint, type, test, and build failures or document an
approved exception. Schema changes and destructive operations require explicit reviewer
attention and a migration or rollback explanation.

## Governance

This constitution is the governing standard for feature specifications, plans, tasks, reviews,
and implementation decisions in this repository. A pull request that violates a principle MUST
either correct the violation or document a specific exception, its risk, and its removal plan
in the plan's Complexity Tracking section.

Amendments MUST be proposed in a reviewed change to this file, explain the motivation and
impact in the Sync Impact Report, and update dependent templates or guidance when their rules
change. Versioning follows semantic versioning: MAJOR for incompatible principle removals or
redefinitions, MINOR for new principles or materially expanded obligations, and PATCH for
clarifications or non-semantic wording changes. Compliance MUST be checked at feature planning,
pull request review, and before release. The latest constitution version and date fields are
authoritative.

**Version**: 1.0.0 | **Ratified**: 2026-09-19 | **Last Amended**: 2026-09-19
