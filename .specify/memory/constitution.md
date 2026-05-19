<!-- SYNC IMPACT REPORT
Version change: N/A → 1.0.0 (initial ratification from template)
Modified principles: none (first authoring)
Added sections:
  - Core Principles (I–V)
  - Technical Constraints
  - Development Workflow
  - Governance
Removed sections: none (template placeholders replaced)
Templates reviewed:
  - .specify/templates/plan-template.md ✅ aligned (Constitution Check gate already present)
  - .specify/templates/spec-template.md ✅ aligned (user stories + acceptance scenarios match principles)
  - .specify/templates/tasks-template.md ✅ aligned (phase structure supports MVP discipline)
Follow-up TODOs: none — all placeholders resolved
-->

# RSSFeedReader-Leandro Constitution

## Core Principles

### I. Security-First

Security is a non-negotiable constraint at every layer of the application, not an afterthought.

- CORS MUST explicitly whitelist known frontend origins; wildcard (`*`) CORS is forbidden in all environments.
- Feed URLs submitted by users MUST be validated as well-formed absolute URIs before any HTTP operation
  (Extended-MVP and beyond); reject malformed or non-HTTP(S) schemes.
- All `HttpClient` calls to external feeds MUST enforce a response timeout of ≤ 10 seconds and MUST
  reject redirects to `localhost`, loopback, or private IP ranges (SSRF prevention).
- Secrets (API keys, connection strings) MUST NOT appear in source code; use .NET User Secrets
  (`dotnet user-secrets`) in development and environment variables in production.
- NuGet/npm dependencies MUST be reviewed for known CVEs before inclusion; run `dotnet list package
  --vulnerable` before each release.

### II. Clean Architecture & Separation of Concerns

The backend and frontend are independent units with a well-defined contract between them.

- The ASP.NET Core Web API project and the Blazor WebAssembly project MUST NOT hold direct project
  references to each other; communication is exclusively via HTTP API calls.
- Business logic MUST reside in dedicated service classes (`*Service.cs`); Razor components only bind
  UI state and delegate to injected services — no logic in code-behind of `.razor` files.
- Shared DTOs or models used by both projects MUST be defined in a shared project or duplicated
  explicitly — no implicit coupling through internal types.
- The Blazor template scaffolding pages (`Counter.razor`, `Weather.razor`, `Home.razor`) MUST be
  deleted and `NavMenu.razor` updated before any MVP feature implementation begins.

### III. Code Quality Standards

All code submitted to the repository MUST meet these non-negotiable quality gates.

- All `.csproj` files MUST include `<TreatWarningsAsErrors>true</TreatWarningsAsErrors>` and
  `<Nullable>enable</Nullable>`; builds with warnings are rejected.
- C# code MUST follow [Microsoft .NET coding conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions):
  PascalCase for types and public members, camelCase for locals and parameters.
- Commented-out code MUST NOT be committed; use `// TODO(author): description` for explicitly
  deferred work only, and resolve TODOs before closing a feature branch.
- All public API controllers and service interfaces MUST have XML documentation comments (`///`).

### IV. Test-First Development (NON-NEGOTIABLE)

Tests are written before or alongside implementation — never deferred to after the fact.

- Every API endpoint MUST have at minimum: one happy-path test and one failure/edge-case test.
- Unit tests for service classes MUST be written before the service is considered complete.
- Tests MUST be organized by user story so each story can be independently verified and demoed.
- Integration tests are required for all Extended-MVP feed-fetching operations (HTTP client + parsing).
- Test projects MUST follow the naming convention `[ProjectName].Tests` and live alongside source.

### V. MVP Discipline (YAGNI)

Scope creep is the primary risk for this project; every line of code must earn its place.

- Only functionality explicitly defined in the current phase (MVP or Extended-MVP) MUST be implemented;
  speculative code for future phases is forbidden.
- In-memory storage (`List<T>`) is the correct and intentional design for MVP; no database, no file
  persistence, and no persistence abstraction until explicitly required by the next phase.
- Feature flags, conditional compilation, or `#if` blocks for future features MUST NOT appear in MVP code.
- Performance optimizations MUST NOT be introduced unless a measurable problem exists; premature
  optimization violates this principle.

## Technical Constraints

These constraints apply to the entire project lifetime and govern technology selection.

- **Platform**: Cross-platform .NET — all code MUST build and run on Windows, macOS, and Linux.
- **Runtime**: .NET 8 (LTS) or later; no framework downgrades permitted without constitution amendment.
- **Backend**: ASP.NET Core Web API with minimal API or controller-based routing; no third-party
  web frameworks.
- **Frontend**: Blazor WebAssembly; no JavaScript frameworks (React, Angular, Vue) introduced without
  explicit approval.
- **Feed parsing** (Extended-MVP+): `System.ServiceModel.Syndication` only; no third-party RSS
  parsing libraries unless `Syndication` is demonstrably insufficient.
- **Local development CORS**: Backend MUST configure CORS for `http://localhost:5XXX` (frontend
  dev port) in `Development` environment; production CORS MUST be explicitly configured.

## Development Workflow

The following sequence is mandatory for every feature; no phase may be skipped.

1. **Branch**: Create a feature branch following the `NNN-feature-name` convention before any work.
2. **Spec → Plan → Tasks**: Complete `/speckit.specify`, `/speckit.plan`, and `/speckit.tasks` in
   order; implementation (`/speckit.implement`) cannot start without an approved `tasks.md`.
3. **Review**: No code is merged to `main` via direct push; all changes require at minimum a
   self-review pass against this constitution's checklist.
4. **Constitution Check**: Every `plan.md` MUST include a Constitution Check section verifying
   compliance with all five core principles before implementation begins.
5. **Cleanup gate**: Blazor template cleanup (Principle II) MUST be verified complete before any
   UI feature task is started.

## Governance

This constitution supersedes all other coding guidelines, README instructions, or verbal agreements.
Conflicts resolve in favor of this document.

- Amendments require: updating this file, incrementing `CONSTITUTION_VERSION` per semantic
  versioning rules, documenting the change in the Sync Impact Report comment, and re-running
  consistency propagation across templates.
- All pull requests MUST include a checklist item confirming compliance with each of the five
  Core Principles.
- Complexity MUST be justified by a requirement in `spec.md` or `plan.md`; complexity without
  a traceable requirement is a violation of Principle V.
- Use `.specify/memory/constitution.md` as the authoritative runtime development reference.

**Version**: 1.0.0 | **Ratified**: 2026-05-19 | **Last Amended**: 2026-05-19
