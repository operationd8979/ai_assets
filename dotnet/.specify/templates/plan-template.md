# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]

**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit-plan` command. See `.specify/templates/plan-template.md` for the execution workflow.

## Summary

[Extract from feature spec: primary requirement + technical approach from research]

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: .NET 10, C# 14

**Primary Dependencies**: ASP.NET Core, Microsoft.FeatureManagement.AspNetCore, Azure.Identity, Microsoft.Identity.Web, Microsoft.AspNetCore.Mvc.Testing; Microsoft.AspNetCore.OData only when the feature adds OData endpoints

**Storage**: N/A for this service by default; configuration and feature flags are sourced from Azure App Configuration

**Testing**: xUnit plus Microsoft.AspNetCore.Mvc.Testing with WebApplicationFactory<Program> for contract and controller-level tests

**Target Platform**: Linux container and Azure hosting

**Project Type**: ASP.NET Core Web API

**Performance Goals**: p95 < 200ms for feature flag and authorization checks unless the feature specifies a stricter target

**Constraints**: No local database or durable state unless justified; auth via Azure AD JWT bearer; Azure App Configuration dependencies must be isolated in tests

**Scale/Scope**: [domain-specific, e.g., 10k users, 1M LOC, 50 screens or NEEDS CLARIFICATION]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [ ] Every new or changed API/OData controller action has a complete contract in `spec.md`.
- [ ] API contracts define method, route, auth, path/query/body inputs, success/error responses, fixture paths, and breaking-change status.
- [ ] OData contracts define entity set/action/function, route template, supported query options, auth, responses, fixture paths, and breaking-change status.
- [ ] Every acceptance scenario maps to a fixture-backed xUnit contract or controller-level unit test in `spec.md`.
- [ ] Contract tests and controller-level unit tests use `Microsoft.AspNetCore.Mvc.Testing` with `WebApplicationFactory<Program>` or a project-specific subclass.
- [ ] External Azure App Configuration, JWT, and network dependencies are replaced by deterministic test configuration or test doubles.
- [ ] The verification command is `dotnet test src/VisionAuthorization.sln`.
- [ ] Any added architectural layer is justified in `## Complexity Tracking`.

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit-plan command output)
├── research.md          # Phase 0 output (/speckit-plan command)
├── data-model.md        # Phase 1 output (/speckit-plan command)
├── quickstart.md        # Phase 1 output (/speckit-plan command)
├── contracts/           # Phase 1 output (/speckit-plan command)
└── tasks.md             # Phase 2 output (/speckit-tasks command - NOT created by /speckit-plan)
```

### Source Code (repository root)

```text
src/
├── VisionAuthorization.sln
├── SCCVision.API.Authorization/
│   ├── Controllers/
│   │   └── [FeatureName]Controller.cs
│   ├── Models/
│   │   └── [DtoOrModel].cs
│   ├── Services/
│   │   └── [FeatureService].cs
│   └── Program.cs
└── SCCVision.API.Authorization.Tests/
    ├── Infrastructure/
    │   └── AuthorizationApiFactory.cs
    ├── Fixtures/
    │   └── [FeatureName]/
    │       ├── [scenario]-request.json
    │       ├── [scenario]-response.json
    │       └── [scenario]-error.json
    ├── Contracts/
    │   └── [FeatureName]ContractTests.cs
    └── Unit/
        └── [FeatureName]ControllerTests.cs
```

**Structure Decision**: Single .NET solution with the API project and one xUnit test
project. Add OData-specific controller folders or files only when the feature requires
OData. Do not add extra layers unless justified in Complexity Tracking.

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |
