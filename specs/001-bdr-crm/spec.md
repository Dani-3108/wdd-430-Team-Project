# Feature Specification: BDR CRM Prospect and Activity Tracking

**Feature Branch**: `001-bdr-crm`  
**Created**: 2026-09-19  
**Status**: Draft  
**Input**: User description: "Create a project specification for a lightweight CRM-style web app for individual B2B sales reps to track their prospects and daily sales activity. Include: a project title and description, the purpose and target audience, user stories for core workflows (sign up, create, read, update, delete), acceptance criteria for each story, API endpoints, and implementation priority."

## Project Overview

### Description

BDR CRM is a lightweight web application that gives individual B2B sales representatives one focused place to manage prospects and record daily sales activity. It provides a personal, easy-to-scan pipeline without the setup and administrative overhead of an enterprise CRM.

### Purpose and Target Audience

The purpose is to help a sales representative quickly answer which prospects need attention, what the current status of each prospect is, and what sales work was completed today. The primary audience is an individual BDR or sales representative managing a personal book of business. Each account is private to its owner in the initial release.

### Implementation Priority

P1 delivers account creation and prospect management so a representative can establish and maintain a usable pipeline. P2 adds daily activity tracking and pipeline visibility. P3 adds safe deletion and account cleanup. Each priority is independently demonstrable, with P1 as the minimum viable product.

## User Scenarios & Testing

### User Story 1 - Create an Account (Priority: P1)

As a new B2B sales representative, I want to sign up for an account so that I can securely manage my own prospects and sales activity.

**Why this priority**: Account ownership is required before prospect and activity data can be created or kept private.

**Independent Test**: Submit valid account details, sign out, sign back in, and verify that the representative reaches an authenticated empty pipeline without seeing another account's data.

**Acceptance Scenarios**:

1. **Given** an unused email address and valid password, **When** the representative submits the sign-up form, **Then** an account is created and the representative is signed in.
2. **Given** an email address already registered, **When** the representative submits sign-up, **Then** the account is not duplicated and a clear corrective message is shown.
3. **Given** invalid or incomplete account details, **When** the representative submits sign-up, **Then** validation errors identify the fields to correct and no account is created.

---

### User Story 2 - Add a Prospect (Priority: P1)

As an authenticated sales representative, I want to create a prospect record so that I can add a new opportunity to my pipeline.

**Why this priority**: Prospect capture is the core action that makes the CRM useful and is needed before daily activity can be associated with a prospect.

**Independent Test**: Sign in, submit a valid prospect, and verify that it appears in the representative's pipeline with the submitted information and initial status.

**Acceptance Scenarios**:

1. **Given** an authenticated representative on the prospect form, **When** they submit a company name, contact name, and valid prospect details, **Then** the prospect is saved with an initial status of `new` and appears in their pipeline.
2. **Given** required prospect information is missing, **When** the representative submits the form, **Then** the record is not saved and the missing fields are identified.
3. **Given** a prospect owned by another representative, **When** the representative attempts to create or associate data with that prospect, **Then** the operation is rejected.

---

### User Story 3 - Review Prospects and Activity (Priority: P2)

As a sales representative, I want to view my prospects and their recent activity so that I can decide what to work on next.

**Why this priority**: A reliable view of the personal pipeline turns stored records into daily sales decisions.

**Independent Test**: Seed prospects and activities for one representative, open the pipeline, and verify that records are listed with status, next action, and recent activity while unrelated records remain hidden.

**Acceptance Scenarios**:

1. **Given** a representative with prospects, **When** they open the pipeline, **Then** their prospects are listed with company, contact, status, next action, and last activity date.
2. **Given** a prospect with recorded activity, **When** the representative opens its details, **Then** activities are shown newest first with type, date, notes, and outcome.
3. **Given** no prospects or activities exist, **When** the representative opens the relevant view, **Then** an empty state explains what can be created next.

---

### User Story 4 - Update Prospect and Activity (Priority: P2)

As a sales representative, I want to update prospect details, status, and activity so that my pipeline reflects the latest conversation and next step.

**Why this priority**: Sales information changes frequently; stale status and next-action data would make the pipeline unreliable.

**Independent Test**: Edit an owned prospect and add or edit its activity, then reload the record and verify that all changes persist and are reflected in the pipeline.

**Acceptance Scenarios**:

1. **Given** an owned prospect, **When** the representative changes its status or contact details with valid values, **Then** the changes are saved and visible on the next read.
2. **Given** an owned prospect, **When** the representative records a completed call, email, or meeting with notes and an outcome, **Then** the activity is saved and becomes the latest activity for that prospect.
3. **Given** invalid update data, **When** the representative submits the update, **Then** the previous valid data remains unchanged and a corrective message is shown.

---

### User Story 5 - Delete Owned Records (Priority: P3)

As a sales representative, I want to delete an obsolete prospect or activity so that my CRM contains only useful, current information.

**Why this priority**: Cleanup is important for trust and data quality, but it is less essential than creating, viewing, and maintaining an active pipeline.

**Independent Test**: Create an owned prospect and activity, delete each through its supported workflow, confirm it no longer appears, and verify that another representative's record cannot be deleted.

**Acceptance Scenarios**:

1. **Given** an owned prospect, **When** the representative confirms deletion, **Then** the prospect and its activities are removed from their pipeline and cannot be retrieved normally.
2. **Given** a deletion request without confirmation, **When** the representative cancels it, **Then** the record remains unchanged.
3. **Given** a prospect owned by another representative, **When** a deletion is requested, **Then** the operation is rejected without revealing whether the record exists.

### Edge Cases

- A duplicate prospect for the same company and contact is allowed, but the interface warns the representative when a likely duplicate is detected.
- A prospect cannot be saved without a company name, contact name, and valid status.
- Activity dates cannot be in the future and activity notes have a bounded maximum length.
- Expired or invalid authentication prevents reads and writes and returns a clear sign-in path rather than partial data.
- Database or network failures preserve unsaved form data where possible and show a retryable error without sensitive details.
- Delete operations require explicit confirmation and are scoped to the authenticated owner.

## Requirements

### Functional Requirements

- **FR-001**: The system MUST allow a new representative to create an account using a unique, valid email address and password.
- **FR-002**: The system MUST authenticate representatives before allowing access to private prospects or activities.
- **FR-003**: The system MUST allow an authenticated representative to create, read, update, and delete only their own prospect records.
- **FR-004**: The system MUST support prospect fields for company name, contact name, email, phone, status, source, notes, next-action description, and next-action date.
- **FR-005**: The system MUST support statuses of `new`, `contacted`, `qualified`, `proposal`, `won`, and `lost`, and MUST reject unsupported statuses.
- **FR-006**: The system MUST allow representatives to create, read, update, and delete activity records associated with their own prospects.
- **FR-007**: The system MUST support activity types of call, email, meeting, and note, with an activity date, notes, and optional outcome.
- **FR-008**: The system MUST enforce ownership checks for every prospect and activity read or mutation and MUST NOT disclose another representative's records.
- **FR-009**: The system MUST validate required fields, field lengths, email formats, dates, and status or activity type values before saving data.
- **FR-010**: The system MUST return consistent success and error responses for the API endpoints defined in this specification.
- **FR-011**: The system MUST provide loading, empty, validation-error, authorization-error, and recoverable-failure states for the core workflows.
- **FR-012**: The system MUST preserve prospect and activity data across sign-out and later sign-in.

### API Endpoints

The API contract uses JSON request and response bodies. All endpoints below require an authenticated representative unless explicitly stated otherwise. Unauthorized requests return `401`; requests for records not owned by the representative return `404` or an equivalent non-disclosing response; invalid input returns `400` with field-level errors.

| Method | Endpoint | Purpose | Success |
| --- | --- | --- | --- |
| POST | `/api/auth/signup` | Create an account and start an authenticated session | `201` |
| POST | `/api/auth/signin` | Authenticate an existing representative | `200` |
| POST | `/api/auth/signout` | End the current session | `204` |
| GET | `/api/prospects` | List owned prospects, with optional status and search filters | `200` |
| POST | `/api/prospects` | Create an owned prospect | `201` |
| GET | `/api/prospects/{prospectId}` | Read one owned prospect and its recent activities | `200` |
| PATCH | `/api/prospects/{prospectId}` | Update owned prospect fields | `200` |
| DELETE | `/api/prospects/{prospectId}` | Delete an owned prospect and associated activities after confirmation | `204` |
| GET | `/api/prospects/{prospectId}/activities` | List activities for an owned prospect | `200` |
| POST | `/api/prospects/{prospectId}/activities` | Create an activity for an owned prospect | `201` |
| PATCH | `/api/activities/{activityId}` | Update an owned activity | `200` |
| DELETE | `/api/activities/{activityId}` | Delete an owned activity after confirmation | `204` |

### Assumptions

- Email and password authentication is sufficient for the initial release; password recovery, invitations, and single sign-on are outside this scope.
- Each representative has one personal workspace; team sharing, roles, and administrator views are outside this scope.
- Records are retained until the representative deletes them; archival and export are outside this scope.
- The API may be consumed by the web interface and future clients, but this specification does not require a public developer API or rate plan.

### Key Entities

- **Representative**: An authenticated individual B2B sales rep identified by email and owning all private CRM records.
- **Prospect**: A company or contact being pursued, including ownership, contact details, pipeline status, notes, and next-action information.
- **Activity**: A dated sales action or note associated with one prospect, including type, notes, outcome, and ownership inherited from the prospect.
- **Session**: The authenticated sign-in state that authorizes a representative to access only their own records.

## Success Criteria

### Measurable Outcomes

- **SC-001**: At least 90% of new representatives complete sign-up and reach an authenticated empty pipeline in under 2 minutes during usability testing.
- **SC-002**: At least 95% of valid prospect create, read, update, and delete operations complete successfully in acceptance testing without data loss.
- **SC-003**: At least 90% of tested representatives can add a prospect and record a daily activity without assistance on their first attempt.
- **SC-004**: At least 99% of authorization tests prevent one representative from reading or mutating another representative's prospect or activity.
- **SC-005**: For a pipeline containing up to 500 prospects, 95% of list and detail views become usable within 2 seconds under normal operating conditions.
- **SC-006**: 100% of supported validation and recoverable failure scenarios provide an actionable message and leave previously saved data unchanged.
