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

**Language/Version**: [e.g., TypeScript 5.9, Angular 21 or NEEDS CLARIFICATION]

**Primary Dependencies**: [e.g., @angular/core, @angular/forms, Angular Material, RxJS or NEEDS CLARIFICATION]

**Storage**: [if applicable, e.g., PostgreSQL, CoreData, files or N/A]

**Testing**: Angular CLI Karma/Jasmine with co-located `*.spec.ts` files; validate with `ng test --watch=false` or NEEDS CLARIFICATION

**Target Platform**: [e.g., browser Angular app, Angular library consumers or NEEDS CLARIFICATION]

**Project Type**: Angular workspace application/library or NEEDS CLARIFICATION

**Performance Goals**: [domain-specific, e.g., 1000 req/s, 10k lines/sec, 60 fps or NEEDS CLARIFICATION]

**Constraints**: [domain-specific, e.g., <200ms p95, <100MB memory, offline-capable or NEEDS CLARIFICATION]

**Scale/Scope**: [domain-specific, e.g., 10k users, 1M LOC, 50 screens or NEEDS CLARIFICATION]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

[Gates determined based on constitution file]

### Stack-Specific Test Gates

**Angular**:

- [ ] Target project and paths are derived from `angular.json`
- [ ] Changed Angular behavior has co-located Karma/Jasmine `*.spec.ts` coverage
- [ ] Tests are planned before implementation tasks for each Angular user story
- [ ] `quickstart.md` validation includes `ng test --watch=false`
- [ ] No alternate unit test runner replaces Angular CLI Karma/Jasmine

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
<!--
  ACTION REQUIRED: Replace the placeholder tree below with the concrete layout
  for this feature. Delete unused options and expand the chosen structure with
  real paths (e.g., apps/admin, packages/something). The delivered plan must
  not include Option labels.
-->

```text
# [REMOVE IF UNUSED] Angular application feature
src/app/[feature]/
├── [feature].component.ts
├── [feature].component.html
├── [feature].component.scss
├── [feature].component.spec.ts
├── [feature].service.ts
└── [feature].service.spec.ts

# [REMOVE IF UNUSED] Angular library feature
projects/[library]/src/lib/[feature]/
├── [feature].component.ts
├── [feature].component.html
├── [feature].component.scss
├── [feature].component.spec.ts
├── [feature].service.ts
├── [feature].service.spec.ts
└── public-api.ts or ../public-api.ts export update when required
```

**Structure Decision**: [Document the selected structure and reference the real
directories captured above]

## Quickstart Validation Requirements

When Angular is in scope, `quickstart.md` MUST include:

- Prerequisites needed to run Angular CLI tests
- The exact validation command: `ng test --watch=false`
- Expected Karma/Jasmine result for the changed `*.spec.ts` files
- Any project-specific command variant only when required by `angular.json`

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |
