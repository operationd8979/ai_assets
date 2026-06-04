# Feature Specification: [FEATURE NAME]

**Feature Branch**: `[###-feature-name]`

**Created**: [DATE]

**Status**: Draft

**Input**: User description: "$ARGUMENTS"

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
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right edge cases.
-->

- What happens when [boundary condition]?
- How does system handle [error scenario]?

## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right functional requirements.
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
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right assumptions based on reasonable defaults
  chosen when the feature description did not specify certain details.
-->

- [Assumption about target users, e.g., "Users have stable internet connectivity"]
- [Assumption about scope boundaries, e.g., "Mobile support is out of scope for v1"]
- [Assumption about data/environment, e.g., "Existing authentication system will be reused"]
- [Dependency on existing system/service, e.g., "Requires access to the existing user profile API"]

## API/OData Contracts *(mandatory for API or OData controller features)*

<!--
  ACTION REQUIRED for every ASP.NET Core API controller or OData controller change.
  Define one contract block per endpoint/action/function. This section is the
  source of truth for contract tests, fixture files, and client integration.

  If the feature does not change API or OData controllers, write:
  "N/A - this feature does not add or change controller endpoints."
-->

### API Endpoint Contract

<!-- Duplicate this block for each non-OData API endpoint. Delete if not applicable. -->

#### `[METHOD] /api/[controller]/[route]`

**Controller action**: `[ControllerName].[ActionName]`

**Auth required**: Yes - Bearer JWT / No

**Path params**: `{paramName}` (type, required/optional) - description / N/A

**Query params**: `?param=` (type, required/optional) - description / N/A

**Request body**:

```json
{
  "field": "type - description"
}
```

*(Use N/A for requests without a body.)*

**Success responses**:

| Status | Content type | Response body |
|--------|--------------|---------------|
| 200 | `application/json` | `{"field": "value"}` |

**Error responses**:

| Status | Condition | Response body |
|--------|-----------|---------------|
| 400 | Invalid input | `{"error": "description"}` |
| 401 | Unauthenticated | *(empty or documented auth error body)* |
| 403 | Unauthorized | `{"error": "description"}` |
| 404 | Resource not found | `{"error": "description"}` |
| 500 | Unexpected server error | `{"error": "An unexpected error occurred."}` |

**Fixture files**:

- Request: `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/[scenario]-request.json` / N/A
- Response: `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/[scenario]-response.json`
- Error response: `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/[scenario]-error.json` / N/A

**Breaking change**: No / Yes - migration path: [describe versioned route, deprecation, or client migration]

### OData Endpoint Contract

<!-- Duplicate this block for each OData entity set, action, or function. Delete if not applicable. -->

#### `[METHOD] /odata/[entitySet]`

**OData surface**: Entity set / Singleton / Action / Function

**Controller action**: `[ODataControllerName].[ActionName]`

**Auth required**: Yes - Bearer JWT / No

**Route template**: `/odata/[entitySet]([key])/[actionOrFunction]` / N/A

**Key params**: `[keyName]` (type, required/optional) - description / N/A

**Supported OData query options**: `$select`, `$filter`, `$orderby`, `$top`, `$skip`, `$count`, `$expand` / N/A

**Request body**:

```json
{
  "field": "type - description"
}
```

*(Use N/A for requests without a body.)*

**Success responses**:

| Status | Content type | Response body |
|--------|--------------|---------------|
| 200 | `application/json` | `{"@odata.context": "...", "value": []}` |

**Error responses**:

| Status | Condition | Response body |
|--------|-----------|---------------|
| 400 | Invalid OData query or input | `{"error": {"message": "description"}}` |
| 401 | Unauthenticated | *(empty or documented auth error body)* |
| 403 | Unauthorized | `{"error": {"message": "description"}}` |
| 404 | Entity or route not found | `{"error": {"message": "description"}}` |
| 500 | Unexpected server error | `{"error": {"message": "An unexpected error occurred."}}` |

**Fixture files**:

- Request: `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/[scenario]-request.json` / N/A
- Response: `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/[scenario]-response.json`
- Error response: `src/SCCVision.API.Authorization.Tests/Fixtures/[FeatureName]/[scenario]-error.json` / N/A

**Breaking change**: No / Yes - migration path: [describe OData model, route, or client migration]

## Test Coverage *(mandatory for API or OData controller features)*

<!--
  One row per Acceptance Scenario. The xUnit test method MUST exist in the
  generated contract or controller-level unit test file. Status values:
  Pending, Failing, Passing.
-->

| # | Scenario | Contract fixture file | xUnit test method | Test type | Status |
|---|----------|-----------------------|-------------------|-----------|--------|
| 1 | Happy path - [describe] | `Fixtures/[Feature]/[scenario]-response.json` | `[MethodName]_Returns[Expected]` | Contract | Pending |
| 2 | Error path - [describe] | `Fixtures/[Feature]/[scenario]-error.json` | `[MethodName]_When[Condition]_Returns[Status]` | Contract | Pending |
| 3 | Controller behavior - [describe] | `Fixtures/[Feature]/[scenario]-response.json` | `[MethodName]_Maps[Behavior]` | Controller unit | Pending |

*Every API/OData contract row MUST be implemented with xUnit. Contract tests and
controller-level unit tests MUST use `Microsoft.AspNetCore.Mvc.Testing` with
`WebApplicationFactory<Program>` or the project-specific test factory.*
