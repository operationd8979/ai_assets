---

description: "Task list template for feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED for every feature. Every Acceptance Scenario in `spec.md` MUST have a corresponding xUnit test. TDD order: fixture files → failing tests → implementation → passing tests.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions (.NET)

- **API source**: `src/SCCVision.API.Authorization/Controllers/`, `src/SCCVision.API.Authorization/`
- **Fixtures**: `src/SCCVision.API.Authorization.Tests/Fixtures/{FeatureName}/`
- **Contract tests**: `src/SCCVision.API.Authorization.Tests/Contracts/{FeatureName}ContractTests.cs`
- **Unit tests**: `src/SCCVision.API.Authorization.Tests/Unit/{FeatureName}Tests.cs`

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit.tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Entities from data-model.md
  - Endpoints from contracts/

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Existing Project Verification)

**Purpose**: Verify existing project structure matches plan.md. For a new feature on an existing project, most tasks here are confirmations, not creations.

- [ ] T001 Verify solution builds: `dotnet build src/VisionAuthorization.sln`
- [ ] T002 Verify existing tests pass baseline: `dotnet test src/VisionAuthorization.sln`
- [ ] T003 [P] Confirm `appsettings.Local.json` has required config keys for this feature

---

## Phase 2: Foundational (Test Infrastructure)

**Purpose**: Test infrastructure that MUST exist before any contract test can be written.

**⚠️ CRITICAL**: No contract test can be written until this phase is complete.

- [ ] T004 Add `Microsoft.AspNetCore.Mvc.Testing` package to test project: `dotnet add src/SCCVision.API.Authorization.Tests/SCCVision.API.Authorization.Tests.csproj package Microsoft.AspNetCore.Mvc.Testing`
- [ ] T005 Create `src/SCCVision.API.Authorization.Tests/Infrastructure/AuthorizationApiFactory.cs` — WebApplicationFactory base class wiring up test host
- [ ] T006 Create `src/SCCVision.API.Authorization.Tests/Infrastructure/TestAuthHandler.cs` — bypass Azure AD JWT for tests using a test auth scheme
- [ ] T007 [P] Create `src/SCCVision.API.Authorization.Tests/Fixtures/` directory structure per spec.md API Contracts

**Checkpoint**: `dotnet test` compiles and runs. Test infrastructure ready — user story work can begin.

---

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 1 (REQUIRED — TDD order) ⚠️

> **Write fixture files and tests FIRST. Confirm they FAIL before writing any implementation code.**

- [ ] T010 [P] [US1] Create fixture files from spec.md API Contracts in `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/`
- [ ] T011 [P] [US1] Contract tests for [endpoint] in `src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs` — assert exact request/response match against fixtures
- [ ] T012 [P] [US1] Unit tests for [service/logic] in `src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]Tests.cs`

### Implementation for User Story 1

- [ ] T013 [P] [US1] Create [model/DTO] in `src/SCCVision.API.Authorization/Models/[Name].cs` (if applicable)
- [ ] T014 [US1] Implement [endpoint] in `src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs` (depends on T013)
- [ ] T015 [US1] Add validation and error handling per contract error table in spec.md
- [ ] T016 [US1] Confirm all T011/T012 tests now pass with `dotnet test`

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 2 (REQUIRED — TDD order) ⚠️

- [ ] T018 [P] [US2] Create fixture files from spec.md API Contracts in `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/`
- [ ] T019 [P] [US2] Contract tests for [endpoint] in `src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs`
- [ ] T020 [P] [US2] Unit tests for [service/logic] in `src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]Tests.cs`

### Implementation for User Story 2

- [ ] T021 [P] [US2] Create [model/DTO] in `src/SCCVision.API.Authorization/Models/[Name].cs` (if applicable)
- [ ] T022 [US2] Implement [endpoint] in `src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs`
- [ ] T023 [US2] Add validation and error handling per contract error table in spec.md
- [ ] T024 [US2] Confirm all T019/T020 tests now pass with `dotnet test`

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 3 (REQUIRED — TDD order) ⚠️

- [ ] T025 [P] [US3] Create fixture files from spec.md API Contracts in `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/`
- [ ] T026 [P] [US3] Contract tests for [endpoint] in `src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs`
- [ ] T027 [P] [US3] Unit tests for [service/logic] in `src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]Tests.cs`

### Implementation for User Story 3

- [ ] T028 [P] [US3] Create [model/DTO] in `src/SCCVision.API.Authorization/Models/[Name].cs` (if applicable)
- [ ] T029 [US3] Implement [endpoint] in `src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs`
- [ ] T030 [US3] Add validation and error handling per contract error table in spec.md
- [ ] T031 [US3] Confirm all T026/T027 tests now pass with `dotnet test`

**Checkpoint**: All user stories should now be independently functional

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] TXXX [P] Documentation updates in docs/
- [ ] TXXX Code cleanup and refactoring
- [ ] TXXX Performance optimization across all stories
- [ ] TXXX [P] Additional unit tests in `src/SCCVision.API.Authorization.Tests/Unit/`
- [ ] TXXX Security hardening
- [ ] TXXX Run quickstart.md validation

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational (Phase 2) - May integrate with US1 but should be independently testable
- **User Story 3 (P3)**: Can start after Foundational (Phase 2) - May integrate with US1/US2 but should be independently testable

### Within Each User Story

- Fixture files MUST exist before tests are written
- Tests MUST be written and FAIL before implementation
- Models/DTOs before controller endpoints
- `dotnet test` MUST pass before story is considered complete
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel (if team capacity allows)
- All tests for a user story marked [P] can run in parallel
- Models within a story marked [P] can run in parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Create fixtures and tests together (all REQUIRED before implementation):
Task: "Create fixture files in Tests/Fixtures/[FeatureName]/"
Task: "Contract tests in Tests/Contracts/[FeatureName]ContractTests.cs"
Task: "Unit tests in Tests/Unit/[FeatureName]Tests.cs"

# After tests are failing, implement in parallel where files don't conflict:
Task: "Create [Model].cs in Controllers or Models/"
Task: "Implement [endpoint] in [Name]Controller.cs"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Test User Story 1 independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable
- Verify tests fail before implementing
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently
- Avoid: vague tasks, same file conflicts, cross-story dependencies that break independence
