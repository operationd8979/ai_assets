# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]

**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/plan-template.md` for the execution workflow.

## Summary

[Extract from feature spec: primary requirement + technical approach from research]

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: .NET 9, C# 13

**Primary Dependencies**: Microsoft.FeatureManagement.AspNetCore, Azure.Identity, Microsoft.Identity.Web, Microsoft.AspNetCore.Mvc.Testing

**Storage**: N/A — stateless API, configuration sourced from Azure App Configuration

**Testing**: xUnit + WebApplicationFactory (integration/contract tests), xUnit standalone (unit tests)

**Target Platform**: Linux container (Docker), Azure App Service / Azure Static Web Apps

**Project Type**: ASP.NET Core Web API — thin delegation layer to Azure App Configuration

**Performance Goals**: p95 < 200ms for feature flag checks

**Constraints**: No database, no local state. Auth via Azure AD JWT Bearer. Config via Azure App Configuration with 5-min refresh interval (configurable).

**Scale/Scope**: [number of frontend clients or NEEDS CLARIFICATION]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

[Gates determined based on constitution file]

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

```text
src/SCCVision.API.Authorization/
├── Controllers/
│   └── [FeatureName]Controller.cs          # new controller (if applicable)
├── HttpContextTargetingContextAccessor.cs
└── Program.cs

src/SCCVision.API.Authorization.Tests/
├── Fixtures/                               # mock data — single source of truth
│   └── [FeatureName]/
│       ├── [scenario]-request.json         # exact request body per contract
│       └── [scenario]-response.json        # exact expected response per contract
├── Contracts/                              # contract tests — assert exact request/response match
│   └── [FeatureName]ContractTests.cs
└── Unit/                                   # unit tests for isolated logic
    └── [FeatureName]Tests.cs
```

**Fixture files are defined in `spec.md → ## API Contracts → Fixture files` and copied here during the test-writing task.**

**Structure Decision**: Single .NET solution with API project + Tests project. No additional layers unless justified in Complexity Tracking.

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |
