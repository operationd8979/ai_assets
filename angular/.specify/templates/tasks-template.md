---

description: "Task list template for Angular feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Angular unit tests are REQUIRED. Every Angular behavior change MUST create or update co-located Karma/Jasmine `*.spec.ts` files and validate with `ng test --watch=false`. Non-Angular tests are included only when required by the feature specification or constitution.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact repository-relative file paths in descriptions
- Replace sample placeholders with concrete Angular paths from `angular.json`

## Path Conventions

- **Angular application**: `src/app/[feature]/`
- **Angular library**: `projects/[library]/src/lib/[feature]/`
- **Angular unit tests**: Co-located `*.spec.ts` next to the component, directive, pipe, service, guard, interceptor, or utility under test
- **Angular validation**: `ng test --watch=false`
- **Library exports**: update `projects/[library]/src/public-api.ts` or the library's existing export entrypoint when required

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit-tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Angular project names, roots, and sourceRoot values from angular.json
  - Entities from data-model.md
  - Contracts or public UI/service interfaces from contracts/

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently with Karma/Jasmine specs
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Confirm Angular workspace targeting and shared prerequisites

- [ ] T001 Confirm target Angular project, root, and sourceRoot in angular.json
- [ ] T002 Identify exact application or library feature path under src/app/ or projects/[library]/src/lib/ in specs/[###-feature-name]/plan.md
- [ ] T003 [P] Review existing Angular testing patterns in adjacent src/app/**/*.spec.ts or projects/[library]/src/lib/**/*.spec.ts files

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Shared Angular infrastructure that MUST be complete before ANY user story can be implemented

**CRITICAL**: No user story work can begin until this phase is complete

Examples of foundational tasks (adjust based on the feature):

- [ ] T004 Establish shared Angular model or interface in projects/[library]/src/lib/[feature]/[feature].model.ts
- [ ] T005 [P] Add shared test fixture or mock data in projects/[library]/src/lib/[feature]/[feature].testing.ts
- [ ] T006 [P] Verify required Angular module/provider/export pattern in angular.json and src/app/app.routes.ts or projects/[library]/src/public-api.ts
- [ ] T007 Update shared public export entrypoint in projects/[library]/src/public-api.ts when multiple stories depend on it

**Checkpoint**: Foundation ready; user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - [Title] (Priority: P1) MVP

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own with its co-located Angular specs]

### Tests for User Story 1 (REQUIRED for Angular)

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T008 [P] [US1] Add failing Karma/Jasmine spec for [component behavior] in src/app/[feature]/[feature].component.spec.ts
- [ ] T009 [P] [US1] Add failing Karma/Jasmine spec for [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.spec.ts

### Implementation for User Story 1

- [ ] T010 [US1] Implement [component behavior] in src/app/[feature]/[feature].component.ts
- [ ] T011 [US1] Implement [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.ts
- [ ] T012 [US1] Wire template and styles in src/app/[feature]/[feature].component.html and src/app/[feature]/[feature].component.scss
- [ ] T013 [US1] Update Angular export, route, module, or provider registration in src/app/app.routes.ts or projects/[library]/src/public-api.ts
- [ ] T014 [US1] Run ng test --watch=false and fix failures for src/app/[feature]/[feature].component.spec.ts and projects/[library]/src/lib/[feature]/[feature].service.spec.ts

**Checkpoint**: User Story 1 is fully functional and testable independently

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own with its co-located Angular specs]

### Tests for User Story 2 (REQUIRED for Angular)

- [ ] T015 [P] [US2] Add failing Karma/Jasmine spec for [component behavior] in src/app/[feature]/[feature].component.spec.ts
- [ ] T016 [P] [US2] Add failing Karma/Jasmine spec for [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.spec.ts

### Implementation for User Story 2

- [ ] T017 [US2] Implement [component behavior] in src/app/[feature]/[feature].component.ts
- [ ] T018 [US2] Implement [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.ts
- [ ] T019 [US2] Integrate with User Story 1 components only through public Angular interfaces in src/app/[feature]/[feature].component.ts and projects/[library]/src/lib/[feature]/[feature].service.ts
- [ ] T020 [US2] Run ng test --watch=false and fix failures for src/app/[feature]/[feature].component.spec.ts and projects/[library]/src/lib/[feature]/[feature].service.spec.ts

**Checkpoint**: User Stories 1 and 2 both work independently

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: [How to verify this story works on its own with its co-located Angular specs]

### Tests for User Story 3 (REQUIRED for Angular)

- [ ] T021 [P] [US3] Add failing Karma/Jasmine spec for [component behavior] in src/app/[feature]/[feature].component.spec.ts
- [ ] T022 [P] [US3] Add failing Karma/Jasmine spec for [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.spec.ts

### Implementation for User Story 3

- [ ] T023 [US3] Implement [component behavior] in src/app/[feature]/[feature].component.ts
- [ ] T024 [US3] Implement [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.ts
- [ ] T025 [US3] Run ng test --watch=false and fix failures for src/app/[feature]/[feature].component.spec.ts and projects/[library]/src/lib/[feature]/[feature].service.spec.ts

**Checkpoint**: All user stories are independently functional

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] TXXX [P] Documentation updates in docs/
- [ ] TXXX Code cleanup and Angular refactoring in src/app/[feature]/ or projects/[library]/src/lib/[feature]/
- [ ] TXXX [P] Additional Karma/Jasmine specs in src/app/[feature]/[feature].component.spec.ts or projects/[library]/src/lib/[feature]/[feature].service.spec.ts
- [ ] TXXX Security and accessibility hardening in src/app/[feature]/[feature].component.html and src/app/[feature]/[feature].component.ts
- [ ] TXXX Run ng test --watch=false for the full Angular workspace validation documented in specs/[###-feature-name]/quickstart.md
- [ ] TXXX Run quickstart.md validation

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies; can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion; BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel when they affect different files
  - Or sequentially in priority order (P1 -> P2 -> P3)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational; no dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational; may integrate with US1 but remains independently testable
- **User Story 3 (P3)**: Can start after Foundational; may integrate with US1/US2 but remains independently testable

### Within Each User Story

- Angular Karma/Jasmine specs MUST be written and fail before implementation
- Models/interfaces before services
- Services before components or consumers
- Component class before template/style integration
- Public exports/routes/providers after implementation
- `ng test --watch=false` before story checkpoint completion

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel within Phase 2
- Once Foundational phase completes, user stories can start in parallel when they do not modify the same files
- All Angular spec tasks for a user story marked [P] can run in parallel
- Different components/services within a story can run in parallel when they live in different files

---

## Parallel Example: User Story 1

```bash
# Launch Angular test tasks for User Story 1 together:
Task: "Add failing Karma/Jasmine spec for [component behavior] in src/app/[feature]/[feature].component.spec.ts"
Task: "Add failing Karma/Jasmine spec for [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.spec.ts"

# Launch independent implementation tasks after specs exist:
Task: "Implement [component behavior] in src/app/[feature]/[feature].component.ts"
Task: "Implement [service behavior] in projects/[library]/src/lib/[feature]/[feature].service.ts"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational
3. Write failing Angular specs for User Story 1
4. Implement User Story 1
5. Run `ng test --watch=false`
6. Stop and validate User Story 1 independently

### Incremental Delivery

1. Complete Setup and Foundational phases
2. Add User Story 1 with co-located specs, then run `ng test --watch=false`
3. Add User Story 2 with co-located specs, then run `ng test --watch=false`
4. Add User Story 3 with co-located specs, then run `ng test --watch=false`
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup and Foundational phases together
2. Once Foundational is done:
   - Developer A: User Story 1 specs and implementation
   - Developer B: User Story 2 specs and implementation
   - Developer C: User Story 3 specs and implementation
3. Stories complete and integrate through public Angular interfaces

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to a specific user story for traceability
- Each Angular user story must be independently completable and testable
- Verify Angular specs fail before implementing behavior
- Use Karma/Jasmine through Angular CLI; do not substitute Jest, Cypress, or Playwright for unit tests
- Stop at any checkpoint to validate story independently
- Avoid vague tasks, missing file paths, same-file parallel conflicts, and cross-story dependencies that break independence
