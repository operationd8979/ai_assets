# Feature Specification: [FEATURE NAME]

**Feature Branch**: `[###-feature-name]`

**Created**: [DATE]

**Status**: Draft

**Input**: User description: "$ARGUMENTS"

**Feature Type**: [API / Angular UI / Full-stack / Library / Other]

**Primary Stack(s)**: [.NET API / Angular / Other - list all that apply]

## User Scenarios & Testing *(mandatory)*

<!--
  IMPORTANT: User stories should be PRIORITIZED as user journeys ordered by importance.
  Each user story/journey must be INDEPENDENTLY TESTABLE - meaning if you implement just ONE of them,
  you should still have a viable MVP (Minimum Viable Product) that delivers value.

  Assign priorities (P1, P2, P3, etc.) to each story, where P1 is the most critical.
  Think of each story as a standalone slice of functionality that can be:
  - Developed independently
  - Tested independently
  - Deployed independently
  - Demonstrated to users independently
-->

### User Story 1 - [Brief Title] (Priority: P1)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently - e.g., "Can be fully tested by [specific action] and delivers [specific value]"]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]
2. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

### User Story 2 - [Brief Title] (Priority: P2)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

### User Story 3 - [Brief Title] (Priority: P3)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

[Add more user stories as needed, each with an assigned priority]

### Edge Cases

<!--
  ACTION REQUIRED: Replace these placeholders with edge cases relevant to the selected stack(s).
  Include API validation and error cases for backend features.
  Include UI states, async loading, empty states, browser behavior, and accessibility cases for Angular features.
-->

- What happens when [boundary condition]?
- How does the system handle [error scenario]?

## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: Replace the content in this section with the right functional requirements.
  Requirements must describe product behavior, not framework implementation details.
-->

### Functional Requirements

- **FR-001**: System MUST [specific capability, e.g., "allow users to create accounts"]
- **FR-002**: System MUST [specific capability, e.g., "validate email addresses"]
- **FR-003**: Users MUST be able to [key interaction, e.g., "reset their password"]
- **FR-004**: System MUST [data requirement, e.g., "persist user preferences"]
- **FR-005**: System MUST [behavior, e.g., "log all security events"]

*Example of marking unclear requirements:*

- **FR-006**: System MUST authenticate users via [NEEDS CLARIFICATION: auth method not specified - email/password, SSO, OAuth?]
- **FR-007**: System MUST retain user data for [NEEDS CLARIFICATION: retention period not specified]

### Key Entities *(include if feature involves data)*

- **[Entity 1]**: [What it represents, key attributes without implementation]
- **[Entity 2]**: [What it represents, relationships to other entities]

## Success Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Define measurable success criteria.
  These must be technology-agnostic and measurable.
-->

### Measurable Outcomes

- **SC-001**: [Measurable metric, e.g., "Users can complete account creation in under 2 minutes"]
- **SC-002**: [Measurable metric, e.g., "System handles 1000 concurrent users without degradation"]
- **SC-003**: [User satisfaction metric, e.g., "90% of users successfully complete primary task on first attempt"]
- **SC-004**: [Business metric, e.g., "Reduce support tickets related to [X] by 50%"]

## Assumptions

<!--
  ACTION REQUIRED: Fill this section with explicit defaults chosen when the feature description did not specify details.
-->

- [Assumption about target users, e.g., "Users have stable internet connectivity"]
- [Assumption about scope boundaries, e.g., "Mobile support is out of scope for v1"]
- [Assumption about data/environment, e.g., "Existing authentication system will be reused"]
- [Dependency on existing system/service, e.g., "Requires access to the existing user profile API"]

## Contracts & Interfaces *(mandatory)*

<!--
  This section is the single source of truth for externally visible behavior.
  Fill only the blocks that apply to this feature type. Delete unused blocks before committing.

  API features: define exact HTTP contracts.
  Angular UI features: define routes, user-visible states, component inputs/outputs, and service dependencies.
  Full-stack features: define both API and UI contracts plus how the UI consumes the API.
-->

### API Contract *(for API or full-stack features)*

#### `[METHOD] /api/[resource]/[route]`

**Auth required**: Yes - [auth scheme] / No

**Path params**: `{paramName}` (type, required) - description / N/A

**Query params**: `?param` (type, optional) - description / N/A

**Request body**:

```json
{
  "field": "type - description"
}
```

*(N/A for requests without a body)*

**Success response**:

```json
{
  "field": "value"
}
```

**Error responses**:

| Status | Condition | Response body |
|--------|-----------|---------------|
| [status] | [condition] | `[body or N/A]` |

**Test data files**:

- `[path/to/feature]/test-data/[scenario]-request.json`
- `[path/to/feature]/test-data/[scenario]-response.json`

**Breaking change**: No / Yes - migration: [describe migration path]

---

### Angular UI Contract *(for Angular UI or full-stack features)*

**Route(s)**: `[path]` - [purpose and navigation entry point]

**Primary component/view**: `[ComponentName]` - [responsibility]

**Inputs**: `[inputName]` (type, required/optional) - description / N/A

**Outputs/events**: `[eventName]` (payload type) - description / N/A

**Service/API dependencies**: `[service or endpoint]` - [expected contract and failure behavior]

**User-visible states**:

| State | Trigger | Expected UI behavior |
|-------|---------|----------------------|
| Loading | [trigger] | [spinner/skeleton/disabled controls/etc.] |
| Empty | [trigger] | [empty state content and available actions] |
| Error | [trigger] | [message, retry behavior, and focus/announcement behavior] |
| Success | [trigger] | [rendered data and next action] |

**Accessibility/responsive requirements**:

- Keyboard access: [required focus order and key behavior]
- Screen reader behavior: [labels, live regions, announcements]
- Responsive behavior: [mobile/tablet/desktop expectations]

**Breaking change**: No / Yes - migration: [describe migration path]

---

### Integration Contract *(for cross-system or full-stack features)*

**Producer**: [system/component/API]

**Consumer**: [system/component/API/UI]

**Data exchanged**: [schema, event, route params, query params, or payload]

**Failure handling**: [retry, fallback, error state, logging, or user message]

**Compatibility requirements**: [versioning, backward compatibility, feature flags, rollout]

## Test Coverage *(mandatory)*

<!--
  One row per Acceptance Scenario defined above.
  Keep this table updated whenever contracts or scenarios change.
  Status: [ ] = not yet implemented, [x] = passing, [!] = failing/blocking

  Test type must match the selected stack:
  - API: contract/integration test, unit test, authorization test
  - Angular: component test, service test, route test, accessibility/responsive check
  - Full-stack: API contract test plus UI integration/component test
-->

| # | Scenario | Contract/interface covered | Test type | Test file or command | Status |
|---|----------|----------------------------|-----------|----------------------|--------|
| 1 | Happy path - [describe] | [API endpoint or UI contract] | [test type] | `[path or command]` | [ ] |
| 2 | Edge case - [describe] | [API endpoint or UI contract] | [test type] | `[path or command]` | [ ] |
| 3 | Invalid input or invalid state | [API endpoint or UI contract] | [test type] | `[path or command]` | [ ] |
| 4 | Unauthorized or unavailable dependency | [API endpoint or UI contract] | [test type] | `[path or command]` | [ ] |

*Add a row for every Acceptance Scenario in the User Stories section. Test names and file paths MUST match the implementation plan and tasks.*
