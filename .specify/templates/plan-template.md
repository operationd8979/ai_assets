# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]

**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/plan-template.md` for the execution workflow.

## Summary

[Extract from feature spec: primary requirement + technical approach from research]

## Technical Context

<!--
  ACTION REQUIRED: Replace placeholders with the technical details for this feature.
  Use the selected stack(s) from spec.md. Delete stack rows and structure examples that do not apply.
-->

**Feature Type**: [API / Angular UI / Full-stack / Library / Other]

**Backend Stack**: [.NET version + C# version / N/A / Other]

**Frontend Stack**: [Angular version + TypeScript version / N/A / Other]

**Primary Dependencies**: [backend packages, Angular libraries, shared SDKs, auth clients]

**Storage/State**: [database, browser storage, server cache, client state, N/A]

**Testing**: [backend test framework and command; Angular Karma/Jasmine via `ng test`; E2E tool if required]

**Target Platform**: [Linux container, browser support matrix, Azure App Service, Azure Static Web Apps, etc.]

**Project Type**: [.NET API / Angular app / Full-stack / Library]

**Performance Goals**: [API latency, UI render budget, bundle budget, interaction budget, or N/A]

**Constraints**: [auth, configuration, browser support, no local state, accessibility, deployment constraints]

**Scale/Scope**: [users, routes, components, endpoints, clients, or NEEDS CLARIFICATION]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

[Gates determined based on constitution file. Include stack-specific gates for contracts, tests, simplicity, and CI commands.]

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

<!--
  ACTION REQUIRED: Replace with actual paths for the selected stack(s).
  Keep only the relevant structure. Do not generate files in paths that do not exist unless the plan explicitly creates them.
-->

#### .NET API structure *(if applicable)*

```text
src/[ApiProject]/
├── Controllers/ or Endpoints/
├── Models/ or Contracts/
├── Services/
└── Program.cs

tests/[ApiProject].Tests/
├── TestData/[FeatureName]/
├── Contracts/
└── Unit/
```

#### Angular UI structure *(if applicable)*

```text
src/app/[feature]/
├── [feature].routes.ts
├── [feature].component.ts
├── [feature].component.html
├── [feature].component.scss
├── [feature].component.spec.ts
├── [feature].service.ts
└── [feature].service.spec.ts
```

#### Full-stack structure *(if applicable)*

```text
src/[ApiProject]/                  # Backend API contracts and implementation
tests/[ApiProject].Tests/          # Backend tests and test data
src/app/[feature]/                 # Angular route, component, service, and tests
```

**Test Data Decision**: [Where request/response JSON, UI fixtures, mocks, or builders live. Must match spec.md contracts.]

**Structure Decision**: [Chosen structure and why it is the simplest fit for this feature. Document any additional layer in Complexity Tracking.]

## Phase 0: Research

<!--
  Resolve unknowns before design. Prefer facts from existing code/config over assumptions.
-->

- [ ] Identify existing project conventions for the selected stack(s)
- [ ] Confirm test runner commands and CI-compatible flags
- [ ] Confirm auth, configuration, routing, and state-management patterns
- [ ] Document decisions in `research.md`

## Phase 1: Design

<!--
  Produce implementation-ready design artifacts.
-->

- [ ] Define contracts/interfaces in `contracts/` and keep them aligned with `spec.md`
- [ ] Define data model, DTOs, view models, or component state in `data-model.md`
- [ ] Define quickstart validation commands in `quickstart.md`
- [ ] Re-run Constitution Check after design

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., extra layer/project/library] | [current need] | [why the simpler structure is insufficient] |
| [e.g., new state-management library] | [specific problem] | [why existing patterns are insufficient] |
