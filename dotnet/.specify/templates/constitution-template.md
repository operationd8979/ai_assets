# SCCVision Authorization API Constitution

## Core Principles

### I. API/OData Contract-First (NON-NEGOTIABLE)
Every ASP.NET Core API controller action and every OData controller endpoint MUST be
specified before implementation begins. The feature spec MUST define the HTTP method
and route or OData entity set/action/function, auth requirements, path/query/body
inputs, response schemas for every status code, error response matrix, fixture file
paths, and breaking-change status. No controller or endpoint implementation task may
start until its contract is complete.

### II. Controller Contract Tests (NON-NEGOTIABLE)
Every API or OData controller action MUST have an xUnit contract test that verifies the
published contract. Contract tests MUST assert status code, relevant headers/content
type, response body, auth behavior, and documented error behavior. Contract payloads
MUST be backed by JSON fixture files instead of inline JSON in test methods.

### III. ASP.NET Core Test Host Discipline (NON-NEGOTIABLE)
Contract tests and controller-level unit tests MUST use
`Microsoft.AspNetCore.Mvc.Testing` with `WebApplicationFactory<Program>` or a
project-specific subclass. The test host MUST replace external Azure App Configuration,
JWT, and network dependencies with deterministic test configuration, test auth, or
test doubles. Pure service/helper unit tests MAY use isolated xUnit tests without
`WebApplicationFactory`.

### IV. Test-First Workflow (NON-NEGOTIABLE)
Tests are REQUIRED for every API or OData controller feature. The required order is:
write fixture files, write failing contract/controller tests, implement the feature,
then make `dotnet test src/VisionAuthorization.sln` pass. Every acceptance scenario in
`spec.md` MUST map to a concrete xUnit test method in the `## Test Coverage` table.

### V. Simplicity and Versioning
This service is a thin authorization and feature-flag API. New repository, CQRS,
mediator, or extra architectural layers are prohibited unless a specific need is
documented in `plan.md` under `## Complexity Tracking`. Breaking changes to existing
API or OData contracts MUST be flagged in the spec and require a migration path, such
as a versioned route or a documented deprecation response.

## Technology Stack

- **Runtime**: .NET 10, ASP.NET Core Web API
- **Controller types**: ASP.NET Core API controllers; OData controllers only when a
  feature explicitly requires OData semantics
- **Feature flags**: Microsoft.FeatureManagement and Azure App Configuration
- **Auth**: Microsoft.Identity.Web with JWT bearer authentication
- **Testing**: xUnit and Microsoft.AspNetCore.Mvc.Testing
- **Test host**: WebApplicationFactory<Program> with deterministic test overrides
- **Test data**: JSON fixture files under `src/SCCVision.API.Authorization.Tests/Fixtures/`
- **CI gate**: `dotnet test src/VisionAuthorization.sln`

## Development Workflow

1. Write `spec.md` with complete `## API/OData Contracts` and `## Test Coverage`
   sections for every API or OData controller change.
2. Create fixture JSON files under
   `src/SCCVision.API.Authorization.Tests/Fixtures/{FeatureName}/`.
3. Write xUnit contract tests and controller-level unit tests using
   `WebApplicationFactory<Program>` and confirm the new tests fail.
4. Implement the controller, OData endpoint, DTOs, services, and validation needed by
   the approved contract.
5. Run `dotnet test src/VisionAuthorization.sln` locally before review.
6. Treat any failed test or missing contract/test mapping as a blocked review item.

## Governance

This constitution supersedes conflicting project practices for specification,
planning, task generation, and review. Every `spec.md`, `plan.md`, and `tasks.md`
artifact MUST pass the constitution checks before implementation starts. Violations
MUST be documented in `plan.md` under `## Complexity Tracking` with rationale and a
simpler rejected alternative.

Amendments require a documented version bump, an updated last-amended date, and
template synchronization for affected Spec Kit artifacts. Major versions indicate
backward-incompatible governance changes, minor versions add or materially expand
principles, and patch versions clarify existing rules.

**Version**: [CONSTITUTION_VERSION] | **Ratified**: [RATIFICATION_DATE] | **Last Amended**: [LAST_AMENDED_DATE]
