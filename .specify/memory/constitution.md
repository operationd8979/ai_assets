# Specification Base Constitution

## Core Principles

### I. Contract-First Interfaces (NON-NEGOTIABLE)

Every externally visible behavior MUST be specified before implementation begins. API features MUST define HTTP method, route, auth, request schema, success response, error responses, and compatibility impact. Angular UI features MUST define routes, user-visible states, component/service boundaries, accessibility expectations, responsive behavior, and API/service dependencies. Full-stack features MUST define both API and UI contracts plus how the UI consumes the API. No implementation task may begin until the relevant contract/interface is complete and reviewed.

### II. Stack-Aware Test Data and Mocks (NON-NEGOTIABLE)

All test data, mocks, and fixtures MUST live in the project-approved location declared by `plan.md`. API tests SHOULD load request/response examples from shared test data when exact payload behavior matters. Angular tests SHOULD use shared mocks, test providers, or builders for services, route data, and async UI states. When a contract or UI state changes, update the corresponding test data/mocks before implementation.

### III. Tests Required - TDD (NON-NEGOTIABLE)

Tests are REQUIRED for every feature. Every Acceptance Scenario in `spec.md` MUST have a stack-appropriate automated test or an explicitly documented manual check when automation is not feasible. API features require backend contract/integration coverage for externally visible behavior. Angular UI features require Karma/Jasmine coverage through `ng test` for component, service, route, and state behavior as applicable. Workflow is strictly: write failing test/check -> review -> implement -> test passes. The `## Test Coverage` table in `spec.md` MUST remain current.

### IV. Simplicity (YAGNI)

Choose the simplest structure that fits the feature and existing project conventions. Do NOT add new layers, state-management libraries, backend abstractions, UI frameworks, generated clients, shared packages, or cross-cutting infrastructure unless a concrete problem demands them and the violation is documented in `plan.md` Complexity Tracking. Existing project patterns are preferred over new abstractions.

### V. Versioning, Compatibility, and Breaking Changes

Breaking changes require explicit migration or compatibility notes. API breaking changes MUST document route/version behavior, response changes, client impact, and migration path. Angular breaking changes MUST document changed routes, component inputs/outputs, navigation behavior, user-visible workflow impact, and migration path. Full-stack breaking changes MUST document both API and UI compatibility.

## Technology Stack Policy

- **Backend**: Use the existing backend runtime, package conventions, project layout, auth, configuration, and CI commands unless `plan.md` justifies a change.
- **Angular UI**: Use the existing Angular workspace conventions, routing style, component style, styling approach, and Karma/Jasmine test setup unless `plan.md` justifies a change.
- **Full-stack**: Stabilize API contracts before wiring Angular services to new or changed backend behavior.
- **Testing**: Use stack-specific commands from `quickstart.md`; Angular Karma/Jasmine features use `ng test --watch=false --browsers=ChromeHeadless` for CI-style validation.
- **CI**: Pull requests are blocked when any required stack-specific build or test command fails.

## Development Workflow

1. Write `spec.md` with feature type, stack, contracts/interfaces, success criteria, assumptions, and Test Coverage complete.
2. Write `plan.md` with technical context, project structure, validation commands, and Constitution Check.
3. Create or update contracts, test data, mocks, and failing tests before implementation.
4. Implement the smallest change that satisfies the failing tests and acceptance scenarios.
5. Run stack-specific validation commands locally before PR.
6. Document complexity, compatibility, or testing exceptions in `plan.md`.

## Governance

This constitution supersedes conflicting generated guidance. All spec, plan, task, checklist, and PR reviews must verify compliance with Principles I-V. Violations must be documented in `plan.md` Complexity Tracking with rationale and the simpler alternative that was rejected. Amendments require a version bump, date, and rationale.

**Version**: 1.1.0 | **Ratified**: 2026-06-03 | **Last Amended**: 2026-06-04
