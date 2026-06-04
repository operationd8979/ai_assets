# SCCVision Authorization API Constitution

## Core Principles

### I. Contract-First (NON-NEGOTIABLE)
Every API endpoint MUST have a contract defined in the feature's `spec.md` under the `## API Contracts` section BEFORE implementation begins. The contract MUST include: HTTP method + route, auth requirements, request schema, response schema for every status code, error response table, and the fixture file paths that will back the tests. No task for implementing an endpoint may begin until its contract is fully defined and reviewed.

### II. Fixture-Driven Testing (NON-NEGOTIABLE)
All mock/test data lives in `src/SCCVision.API.Authorization.Tests/Fixtures/{FeatureName}/` as JSON files. Test code MUST NOT inline request or response data — it MUST load from fixture files. When a contract changes, the corresponding fixture file MUST be updated first, before any code changes. This ensures the fixture directory is the single source of truth for expected API behavior.

### III. Tests Bắt Buộc — TDD (NON-NEGOTIABLE)
Tests are REQUIRED for every feature, not optional. Every Acceptance Scenario in `spec.md` MUST have a corresponding xUnit test in `ContractTests.cs`. Workflow is strictly: write failing test → get review → implement → test passes. The `## Test Coverage` table in `spec.md` MUST be kept up to date at all times. `dotnet test` passing is a hard CI gate — PRs that fail tests are blocked.

### IV. Simplicity (YAGNI)
This is a thin API: Azure App Configuration → Feature Management → HTTP response. Do NOT add unnecessary layers. Repository pattern, CQRS, mediator, and similar patterns are prohibited unless a concrete problem demands them and the violation is documented in the `Complexity Tracking` section of `plan.md`. Controllers call services or Azure SDK directly.

### V. Versioning & Breaking Changes
Breaking changes to existing endpoints require a deprecation period. The old endpoint MUST return `410 Gone` with a JSON body containing a `migrationUrl` or `message` field pointing to the new endpoint. Major version bumps use route versioning (`/api/v2/`). Minor non-breaking additions do not require a new version. Every breaking change MUST be flagged in the `**Breaking change**` field of the API Contracts section in `spec.md`.

## Technology Stack

- **Runtime**: .NET 9, ASP.NET Core Web API
- **Feature Flags**: Microsoft.FeatureManagement + Azure App Configuration
- **Auth**: Microsoft.Identity.Web (Azure AD / JWT Bearer)
- **Testing**: xUnit, Microsoft.AspNetCore.Mvc.Testing (WebApplicationFactory)
- **Test data**: JSON fixture files in `Tests/Fixtures/`
- **CI**: Azure Pipelines — `dotnet test` is a required passing gate

## Development Workflow

1. `spec.md` written with `## API Contracts` and `## Test Coverage` sections complete
2. Fixture JSON files created in `Tests/Fixtures/{FeatureName}/`
3. xUnit tests written and confirmed FAILING
4. Implementation written until tests pass
5. `dotnet test` run locally before PR
6. PR blocked if CI fails

## Governance

This constitution supersedes all other practices. Violations must be documented in the `Complexity Tracking` section of `plan.md` with justification. All PRs and spec reviews must verify compliance with Principles I–V. Amendments to this constitution require a note in the `## Governance` section with date and rationale.

**Version**: 1.0.0 | **Ratified**: 2026-06-03 | **Last Amended**: 2026-06-03
