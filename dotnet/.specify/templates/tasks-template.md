---

description: "Task list template for .NET API/OData feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED for every ASP.NET Core API or OData controller feature. Required order: fixture files -> failing xUnit contract/controller tests -> implementation -> passing `dotnet test src/VisionAuthorization.sln`.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions (.NET)

- **Solution**: `src/VisionAuthorization.sln`
- **API source**: `src/SCCVision.API.Authorization/`
- **API controllers**: `src/SCCVision.API.Authorization/Controllers/`
- **OData controllers**: `src/SCCVision.API.Authorization/Controllers/` or the feature-specific folder chosen in plan.md
- **Test project**: `src/SCCVision.API.Authorization.Tests/`
- **Test infrastructure**: `src/SCCVision.API.Authorization.Tests/Infrastructure/`
- **Fixtures**: `src/SCCVision.API.Authorization.Tests/Fixtures/{FeatureName}/`
- **Contract tests**: `src/SCCVision.API.Authorization.Tests/Contracts/{FeatureName}ContractTests.cs`
- **Controller unit tests**: `src/SCCVision.API.Authorization.Tests/Unit/{FeatureName}ControllerTests.cs`

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit-tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - API/OData Contracts and Test Coverage sections from spec.md
  - Feature requirements from plan.md
  - Entities from data-model.md
  - Endpoint contracts from contracts/

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Existing .NET Project Verification)

**Purpose**: Confirm the current solution and test project are ready for API/OData contract testing.

- [ ] T001 Verify solution builds with `dotnet build src/VisionAuthorization.sln`
- [ ] T002 Verify baseline tests run with `dotnet test src/VisionAuthorization.sln`
- [ ] T003 [P] Confirm API project targets .NET 10 in `src/SCCVision.API.Authorization/SCCVision.API.Authorization.csproj`
- [ ] T004 [P] Confirm test project targets .NET 10 in `src/SCCVision.API.Authorization.Tests/SCCVision.API.Authorization.Tests.csproj`

---

## Phase 2: Foundational (Contract Test Infrastructure)

**Purpose**: Test infrastructure that MUST exist before any user story implementation begins.

**CRITICAL**: No API or OData controller implementation can begin until this phase is complete.

- [ ] T005 Add `Microsoft.AspNetCore.Mvc.Testing` to `src/SCCVision.API.Authorization.Tests/SCCVision.API.Authorization.Tests.csproj` if missing
- [ ] T006 Add a project reference from `src/SCCVision.API.Authorization.Tests/SCCVision.API.Authorization.Tests.csproj` to `src/SCCVision.API.Authorization/SCCVision.API.Authorization.csproj` if missing
- [ ] T007 Create or update `src/SCCVision.API.Authorization.Tests/Infrastructure/AuthorizationApiFactory.cs` using `WebApplicationFactory<Program>`
- [ ] T008 Create or update test auth/configuration overrides in `src/SCCVision.API.Authorization.Tests/Infrastructure/` to isolate Azure App Configuration, JWT, and network dependencies
- [ ] T009 [P] Create `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/` from `spec.md` API/OData contract fixture paths

**Checkpoint**: `dotnet test src/VisionAuthorization.sln` compiles and runs with the shared test host.

---

## Phase 3: User Story 1 - [Title] (Priority: P1)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 1 (REQUIRED - TDD order)

> Write fixture files and tests FIRST. Confirm the new tests fail before writing implementation code.

- [ ] T010 [P] [US1] Create request/response/error fixture files for [endpoint or OData route] in `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/`
- [ ] T011 [P] [US1] Add xUnit contract tests for [endpoint or OData route] in `src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs` using `WebApplicationFactory<Program>`
- [ ] T012 [P] [US1] Add controller-level unit tests for [controller action] in `src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]ControllerTests.cs` using the shared test factory

### Implementation for User Story 1

- [ ] T013 [P] [US1] Create or update DTO/model types in `src/SCCVision.API.Authorization/[Models or feature folder]/`
- [ ] T014 [US1] Implement [API controller action or OData endpoint] in `src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs`
- [ ] T015 [US1] Add validation, auth behavior, and documented error responses from `spec.md`
- [ ] T016 [US1] Update `spec.md` Test Coverage status for US1 test methods
- [ ] T017 [US1] Confirm US1 passes with `dotnet test src/VisionAuthorization.sln`

**Checkpoint**: User Story 1 is fully functional and testable independently.

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 2 (REQUIRED - TDD order)

- [ ] T018 [P] [US2] Create request/response/error fixture files for [endpoint or OData route] in `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/`
- [ ] T019 [P] [US2] Add xUnit contract tests for [endpoint or OData route] in `src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs` using `WebApplicationFactory<Program>`
- [ ] T020 [P] [US2] Add controller-level unit tests for [controller action] in `src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]ControllerTests.cs` using the shared test factory

### Implementation for User Story 2

- [ ] T021 [P] [US2] Create or update DTO/model types in `src/SCCVision.API.Authorization/[Models or feature folder]/`
- [ ] T022 [US2] Implement [API controller action or OData endpoint] in `src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs`
- [ ] T023 [US2] Add validation, auth behavior, and documented error responses from `spec.md`
- [ ] T024 [US2] Update `spec.md` Test Coverage status for US2 test methods
- [ ] T025 [US2] Confirm US2 passes with `dotnet test src/VisionAuthorization.sln`

**Checkpoint**: User Stories 1 and 2 work independently.

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own]

### Tests for User Story 3 (REQUIRED - TDD order)

- [ ] T026 [P] [US3] Create request/response/error fixture files for [endpoint or OData route] in `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/`
- [ ] T027 [P] [US3] Add xUnit contract tests for [endpoint or OData route] in `src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs` using `WebApplicationFactory<Program>`
- [ ] T028 [P] [US3] Add controller-level unit tests for [controller action] in `src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]ControllerTests.cs` using the shared test factory

### Implementation for User Story 3

- [ ] T029 [P] [US3] Create or update DTO/model types in `src/SCCVision.API.Authorization/[Models or feature folder]/`
- [ ] T030 [US3] Implement [API controller action or OData endpoint] in `src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs`
- [ ] T031 [US3] Add validation, auth behavior, and documented error responses from `spec.md`
- [ ] T032 [US3] Update `spec.md` Test Coverage status for US3 test methods
- [ ] T033 [US3] Confirm US3 passes with `dotnet test src/VisionAuthorization.sln`

**Checkpoint**: All selected user stories are independently functional.

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories.

- [ ] TXXX [P] Add isolated xUnit tests for pure services/helpers in `src/SCCVision.API.Authorization.Tests/Unit/` when business logic exists outside controllers
- [ ] TXXX [P] Update documentation or quickstart examples affected by the API/OData contract
- [ ] TXXX Refactor duplicated test setup into `src/SCCVision.API.Authorization.Tests/Infrastructure/`
- [ ] TXXX Confirm all contract fixture files are referenced by tests and `spec.md`
- [ ] TXXX Run `dotnet test src/VisionAuthorization.sln`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies; validates current solution state
- **Foundational (Phase 2)**: Depends on Setup; blocks all API/OData user story work
- **User Stories (Phase 3+)**: Depend on Foundational phase completion
- **Polish (Final Phase)**: Depends on all selected user stories

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational; no dependency on later stories
- **User Story 2 (P2)**: Can start after Foundational; may integrate with US1 but remains independently testable
- **User Story 3 (P3)**: Can start after Foundational; may integrate with US1/US2 but remains independently testable

### Within Each User Story

- Fixture files before tests
- Contract tests and controller-level unit tests before implementation
- New tests MUST fail before implementation
- DTOs/models before controller endpoint changes
- Validation and error handling before the story checkpoint
- `dotnet test src/VisionAuthorization.sln` MUST pass before story completion

### Parallel Opportunities

- Setup verification tasks marked [P] can run in parallel
- Fixture creation and test files for the same story can run in parallel when they touch different files
- Contract tests for different endpoints can run in parallel
- Different user stories can proceed in parallel after Foundational, if their controller/source files do not conflict

---

## Parallel Example: User Story 1

```bash
# Create fixtures and tests first:
Task: "Create fixture files in src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/"
Task: "Add contract tests in src/SCCVision.API.Authorization.Tests/Contracts/[FeatureName]ContractTests.cs"
Task: "Add controller tests in src/SCCVision.API.Authorization.Tests/Unit/[FeatureName]ControllerTests.cs"

# After tests fail for the expected reason, implement non-conflicting files:
Task: "Create DTO/model types in src/SCCVision.API.Authorization/[Models or feature folder]/"
Task: "Implement controller action in src/SCCVision.API.Authorization/Controllers/[Name]Controller.cs"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1 setup verification.
2. Complete Phase 2 test infrastructure.
3. Create US1 fixtures and failing tests.
4. Implement US1 until tests pass.
5. Stop and validate US1 independently with `dotnet test src/VisionAuthorization.sln`.

### Incremental Delivery

1. Build shared test host once.
2. Add each user story as contracts + fixtures + failing tests.
3. Implement only the code needed for that story.
4. Run `dotnet test src/VisionAuthorization.sln` at each checkpoint.
5. Keep `spec.md` Test Coverage synchronized with implemented tests.

---

## Notes

- [P] tasks = different files, no dependencies
- Every API/OData controller action needs a contract test
- Contract and controller-level tests use `Microsoft.AspNetCore.Mvc.Testing`
- Pure service/helper unit tests can be isolated xUnit tests
- Avoid inline JSON payloads in contract tests; use fixture files
- Avoid extra architectural layers unless justified in Complexity Tracking
