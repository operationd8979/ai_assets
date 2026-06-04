---

description: "Task list template for feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED for every feature. Every Acceptance Scenario in `spec.md` MUST have a corresponding stack-appropriate test. TDD order: test data/mocks -> failing tests -> implementation -> passing tests.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions
- Include the stack in tasks when a feature spans more than one stack: `[API]`, `[Angular]`, or `[Full-stack]`

## Path Conventions by Stack

<!--
  ACTION REQUIRED: Keep only the path conventions that apply to this feature.
  Replace placeholders with actual project paths from plan.md.
-->

### .NET API

- **API source**: `src/[ApiProject]/`
- **API contracts/models**: `src/[ApiProject]/[Contracts|Models]/`
- **API tests**: `tests/[ApiProject].Tests/`
- **API test data**: `tests/[ApiProject].Tests/TestData/{FeatureName}/`

### Angular UI

- **Feature source**: `src/app/[feature]/`
- **Component tests**: `src/app/[feature]/[feature].component.spec.ts`
- **Service tests**: `src/app/[feature]/[feature].service.spec.ts`
- **UI test data/mocks**: `src/app/[feature]/testing/` or existing project convention

### Full-stack

- Use both API and Angular conventions.
- Keep API contract tasks before Angular service integration tasks when the UI consumes new or changed endpoints.

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit.tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Entities from data-model.md
  - Contracts from contracts/
  - Selected stack(s): .NET API, Angular UI, full-stack, or other

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Existing Project Verification)

**Purpose**: Verify existing project structure, dependencies, and baseline commands match plan.md.

- [ ] T001 Confirm selected stack(s), project paths, and test commands from `plan.md`
- [ ] T002 Verify build command from `quickstart.md` succeeds for the selected stack(s)
- [ ] T003 Verify baseline test command from `quickstart.md` succeeds before changes
- [ ] T004 [P] Confirm required configuration, environment files, and secrets are documented without committing secrets

### Angular setup commands *(use when Angular applies)*

- [ ] T005 [Angular] Install dependencies with `npm ci`
- [ ] T006 [Angular] Verify production-safe build with `ng build`
- [ ] T007 [Angular] Verify Karma/Jasmine baseline with `ng test --watch=false --browsers=ChromeHeadless`

---

## Phase 2: Foundational (Test and Contract Infrastructure)

**Purpose**: Create or verify infrastructure that MUST exist before user story tests can be written.

**CRITICAL**: No story implementation should begin until this phase is complete.

- [ ] T008 Confirm contract/interface definitions from `spec.md` are represented in `contracts/`
- [ ] T009 [P] Create or verify test data/mocks directory for this feature
- [ ] T010 [API] Create or verify backend contract test base/helpers using the existing test framework
- [ ] T011 [Angular] Create or verify Angular testing helpers, service mocks, route test setup, and shared providers
- [ ] T012 [Full-stack] Confirm API contract changes are consumable by the Angular service layer

**Checkpoint**: Stack-specific test commands compile and run. User story work can begin.

---

## Phase 3: User Story 1 - [Title] (Priority: P1) - MVP

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 1 (REQUIRED - TDD order)

> Write test data/mocks and tests FIRST. Confirm they FAIL before writing implementation code.

- [ ] T013 [P] [US1] Create test data or mocks for [scenario] in `[test-data-or-mocks-path]`
- [ ] T014 [API] [US1] Add backend contract/integration test for [endpoint or behavior] in `[api-test-path]`
- [ ] T015 [API] [US1] Add backend unit test for [service/logic] in `[api-unit-test-path]`
- [ ] T016 [Angular] [US1] Add component test for [view behavior] in `[component-spec-path]`
- [ ] T017 [Angular] [US1] Add service or route test for [dependency/navigation behavior] in `[service-or-route-spec-path]`

### Implementation for User Story 1

- [ ] T018 [API] [US1] Implement or update API contract/model in `[api-contract-or-model-path]`
- [ ] T019 [API] [US1] Implement endpoint/service behavior in `[api-source-path]`
- [ ] T020 [Angular] [US1] Implement or update component/template/styles in `[angular-feature-path]`
- [ ] T021 [Angular] [US1] Implement or update service integration in `[angular-service-path]`
- [ ] T022 [US1] Add validation, error handling, loading/empty/error UI states per spec.md
- [ ] T023 [US1] Confirm stack-specific tests for US1 now pass

**Checkpoint**: User Story 1 is fully functional and testable independently.

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 2 (REQUIRED - TDD order)

- [ ] T024 [P] [US2] Create test data or mocks for [scenario] in `[test-data-or-mocks-path]`
- [ ] T025 [API] [US2] Add backend contract/integration test for [endpoint or behavior] in `[api-test-path]`
- [ ] T026 [Angular] [US2] Add component/service/route test for [UI behavior] in `[angular-spec-path]`

### Implementation for User Story 2

- [ ] T027 [API] [US2] Implement backend behavior in `[api-source-path]`
- [ ] T028 [Angular] [US2] Implement Angular behavior in `[angular-feature-path]`
- [ ] T029 [US2] Confirm stack-specific tests for US2 now pass

**Checkpoint**: User Stories 1 and 2 work independently.

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 3 (REQUIRED - TDD order)

- [ ] T030 [P] [US3] Create test data or mocks for [scenario] in `[test-data-or-mocks-path]`
- [ ] T031 [API] [US3] Add backend contract/integration test for [endpoint or behavior] in `[api-test-path]`
- [ ] T032 [Angular] [US3] Add component/service/route test for [UI behavior] in `[angular-spec-path]`

### Implementation for User Story 3

- [ ] T033 [API] [US3] Implement backend behavior in `[api-source-path]`
- [ ] T034 [Angular] [US3] Implement Angular behavior in `[angular-feature-path]`
- [ ] T035 [US3] Confirm stack-specific tests for US3 now pass

**Checkpoint**: All planned user stories work independently.

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories.

- [ ] TXXX [P] Documentation updates in docs or feature quickstart
- [ ] TXXX Code cleanup and refactoring
- [ ] TXXX Performance optimization across all affected stacks
- [ ] TXXX [API] Additional backend tests for shared logic or cross-cutting behavior
- [ ] TXXX [Angular] Additional accessibility, responsive, and component edge-case tests
- [ ] TXXX Security hardening and authorization checks
- [ ] TXXX Run quickstart.md validation end to end

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - blocks all user stories
- **User Stories (Phase 3+)**: Depend on Foundational phase completion
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational - no dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational - may integrate with US1 but must be independently testable
- **User Story 3 (P3)**: Can start after Foundational - may integrate with US1/US2 but must be independently testable

### Within Each User Story

- Test data/mocks MUST exist before tests are written
- Tests MUST be written and FAIL before implementation
- Contracts/interfaces before consumers
- API changes before Angular service integration when the UI depends on new backend behavior
- Stack-specific test commands MUST pass before a story is considered complete
- Story complete before moving to the next priority unless parallel staffing is explicit

### Parallel Opportunities

- Setup tasks marked [P] can run in parallel
- Foundational tasks marked [P] can run in parallel when they touch different stacks
- Tests for a user story can run in parallel when they touch different files
- API and Angular implementation can run in parallel after contracts are stable
- Different user stories can be worked on in parallel by different team members after Foundational completes

---

## Parallel Example: User Story 1

```text
# Create test data and tests first:
Task: "Create test data/mocks in [test-data-or-mocks-path]"
Task: "Add API contract test in [api-test-path]"
Task: "Add Angular component test in [component-spec-path]"

# After tests are failing, implement where files do not conflict:
Task: "Implement API behavior in [api-source-path]"
Task: "Implement Angular component and service behavior in [angular-feature-path]"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational
3. Complete Phase 3: User Story 1
4. Stop and validate User Story 1 independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational
2. Add User Story 1 -> test independently -> deploy/demo
3. Add User Story 2 -> test independently -> deploy/demo
4. Add User Story 3 -> test independently -> deploy/demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to a specific user story for traceability
- Each user story should be independently completable and testable
- Verify tests fail before implementing
- Commit after each task or logical group if the project workflow requires it
- Stop at any checkpoint to validate story independently
- Avoid vague tasks, same-file conflicts, and hidden cross-story dependencies
