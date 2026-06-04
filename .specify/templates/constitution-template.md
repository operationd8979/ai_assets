# [PROJECT_NAME] Constitution

## Core Principles

### I. Contract-First Interfaces (NON-NEGOTIABLE)

Every externally visible behavior MUST be specified before implementation begins. API features define HTTP contracts. Angular UI features define routes, user-visible states, component/service boundaries, accessibility expectations, and API/service dependencies. Full-stack features define both sides plus their integration contract. No implementation task may begin until the relevant contract/interface is complete and reviewed.

### II. Stack-Aware Test Data and Mocks (NON-NEGOTIABLE)

Test data, mocks, and fixtures MUST live in the project-approved location declared by `plan.md`. Test code SHOULD load or build test data from shared test helpers instead of scattering inline payloads across unrelated tests. When a contract or UI state changes, update the corresponding test data/mocks before implementation.

### III. Tests Required - TDD (NON-NEGOTIABLE)

Tests are REQUIRED for every feature. Every Acceptance Scenario in `spec.md` MUST have a stack-appropriate automated test or an explicitly documented manual check when automation is not feasible. Workflow is strictly: write failing test/check -> review -> implement -> test passes. The `## Test Coverage` table in `spec.md` MUST remain current.

### IV. Simplicity (YAGNI)

Choose the simplest structure that fits the feature and existing project conventions. Do not add new layers, state-management libraries, backend abstractions, UI frameworks, generated clients, or shared packages unless a concrete problem demands them and the violation is documented in `plan.md` Complexity Tracking.

### V. Versioning, Compatibility, and Breaking Changes

Breaking changes require explicit migration or compatibility notes. API breaking changes MUST document route/version behavior and client impact. Angular breaking changes MUST document changed routes, component inputs/outputs, navigation behavior, or user-visible workflow impact. Full-stack changes MUST document both API and UI compatibility.

## Technology Stack Policy

- **Backend**: Use the existing backend stack and project layout unless `plan.md` justifies a change.
- **Angular UI**: Use the existing Angular workspace conventions, routing style, component style, and Karma/Jasmine test setup unless `plan.md` justifies a change.
- **Full-stack**: Keep API contracts stable before wiring Angular services to new or changed backend behavior.
- **Testing**: Use stack-specific test commands from `quickstart.md`; Angular Karma/Jasmine features use `ng test --watch=false --browsers=ChromeHeadless` for CI-style validation.
- **CI**: Pull requests are blocked when any required stack-specific build or test command fails.

## Development Workflow

1. Write `spec.md` with stack, contracts/interfaces, success criteria, and Test Coverage complete.
2. Write `plan.md` with technical context, project structure, test commands, and Constitution Check.
3. Create or update contracts, test data, mocks, and failing tests before implementation.
4. Implement the smallest change that satisfies the failing tests and acceptance scenarios.
5. Run stack-specific validation commands locally before PR.
6. Document any complexity or compatibility exception in `plan.md`.

## Governance

This constitution supersedes conflicting generated guidance. All spec, plan, task, and PR reviews must verify compliance with Principles I-V. Violations must be documented in `plan.md` Complexity Tracking with rationale and the simpler alternative that was rejected. Amendments require a version bump, date, and rationale.

**Version**: [CONSTITUTION_VERSION] | **Ratified**: [RATIFICATION_DATE] | **Last Amended**: [LAST_AMENDED_DATE]
