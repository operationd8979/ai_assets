<!--
Sync Impact Report
Version change: template baseline -> 1.0.1
Modified principles:
- Angular Workspace Source
- Co-Located Angular Unit Tests
- Karma/Jasmine Validation
- Precise Spec-Driven Tasks
- Independent User Story Delivery
Added sections:
- Angular Delivery Constraints
- Spec-Driven Workflow Gates
Removed sections:
- Generic placeholder sections
Templates requiring updates:
- REVIEW .specify/templates/plan-template.md
- REVIEW .specify/templates/spec-template.md
- REVIEW .specify/templates/tasks-template.md
Follow-up TODOs: None
-->
# SCCVision Presentation Libs Constitution

## Core Principles

### I. Angular Workspace Source

The Angular workspace configuration in `angular.json` is the source of truth for
project names, roots, source roots, builders, and test targets. Plans and tasks
MUST derive target paths from that workspace configuration before choosing files.
Angular feature work MUST target either the application tree under `src/app/**`
or a library tree under `projects/<library>/src/lib/**`. If the target project
or source root cannot be derived, planning MUST mark it as `NEEDS CLARIFICATION`
instead of inventing a path.

### II. Co-Located Angular Unit Tests

Every Angular behavior change MUST add or update a co-located `*.spec.ts` file
before implementation code is written. Components, directives, pipes, services,
guards, interceptors, and shared utilities MUST have their observable behavior
covered in the adjacent Karma/Jasmine spec. Generated tasks MUST place the unit
test task before the implementation task for the same user story.

### III. Karma/Jasmine Validation

Angular unit validation MUST use Angular CLI with Karma/Jasmine. The required
validation command is `ng test --watch=false`. Jest, Cypress, Playwright, or
manual browser checks MUST NOT replace Karma/Jasmine unit tests for spec-driven
Angular features. Additional integration or end-to-end checks may be added only
as supplemental validation.

### IV. Precise Spec-Driven Tasks

Generated `plan.md`, `quickstart.md`, and `tasks.md` artifacts MUST include
exact repository-relative Angular paths. `tasks.md` MUST identify the specific
`*.spec.ts` files to create or update and MUST include a validation task that
runs `ng test --watch=false`. Vague tasks such as "add tests" or "update UI"
are not acceptable.

### V. Independent User Story Delivery

Each user story MUST remain independently buildable and testable. A story phase
MUST include the Angular unit tests and implementation needed to demonstrate
that story without requiring later stories. Shared setup belongs in the
foundational phase only when it blocks multiple stories.

## Angular Delivery Constraints

Angular features in this repository use TypeScript, Angular CLI, and
Karma/Jasmine. Spec-driven output MUST prefer existing workspace conventions:
SCSS component styles, co-located Angular specs, library code under
`projects/<library>/src/lib/**`, and application code under `src/app/**`.
Generated plans MUST not introduce an alternate unit test runner unless the
constitution is amended first.

## Spec-Driven Workflow Gates

The Constitution Check in `plan.md` MUST verify Angular workspace path
selection, required co-located `*.spec.ts` coverage, and `ng test --watch=false`
validation. The task generation step MUST preserve test-before-code ordering
for Angular stories. Feature specifications for Angular work MUST describe
independent tests in observable UI or service behavior terms so task generation
can map them to concrete `*.spec.ts` files. The implementation step MUST not
mark Angular tasks complete until the relevant spec files exist and Angular
unit validation has been run or a blocking reason is recorded.

## Governance

This constitution supersedes other local Spec Kit guidance for Angular feature
planning and task generation. Amendments MUST include a semantic version bump,
an updated Sync Impact Report, and synchronization with dependent templates.
Reviewers MUST reject generated plans or task lists that omit required
co-located Angular unit tests or omit `ng test --watch=false` validation.

Versioning follows semantic versioning: MAJOR for incompatible governance
changes, MINOR for new principles or materially expanded rules, and PATCH for
clarifications. This template is ratified as the baseline for SCCVision
Presentation Libs Angular Spec Kit governance.

**Version**: 1.0.1 | **Ratified**: 2026-06-04 | **Last Amended**: 2026-06-04
