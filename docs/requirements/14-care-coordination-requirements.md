# Care Coordination - Requirements Specification
**Capability ID:** 14 | **Version:** 1.0 | **Last Updated:** 2025-11-15

## Functional Requirements
### FR-CC-001: Care Plan Management
**Description:** System shall support care plan creation and tracking.
**Acceptance Criteria:** Care plan creation | Goals documentation | Interventions tracking | Care plan review dates | Multi-provider collaboration | Patient involvement

### FR-CC-002: Care Team Assignment
**Description:** System shall define care teams.
**Acceptance Criteria:** Team members assignable | Roles definable (primary, specialist, coordinator) | Care team viewable in chart | Communication within team

### FR-CC-003: Referral Management
**Description:** System shall track referrals.
**Acceptance Criteria:** Referral creation | Referral reasons | Specialist assignment | Status tracking (pending, completed, cancelled) | Referral reports

### FR-CC-004: Transition of Care Documents
**Description:** System shall generate transition documents.
**Acceptance Criteria:** CCDA for transitions | Referral summary | Discharge summary | Send via Direct messaging | Patient portal download

### FR-CC-005: Patient Goals
**Description:** System shall track patient health goals.
**Acceptance Criteria:** Goal creation | Target dates | Progress tracking | Goal categories | Goal status (active, achieved, abandoned)

## Non-Functional Requirements
### NFR-CC-001: Interoperability
**Description:** Care coordination shall use standards.
**Acceptance Criteria:** CCDA for transitions | FHIR CarePlan resource | FHIR Goal resource | FHIR CareTeam resource

### NFR-CC-002: Collaboration
**Description:** Multiple providers shall collaborate.
**Acceptance Criteria:** Shared care plans | Real-time updates | Role-based access | Communication tools

### NFR-CC-003: Patient Engagement
**Description:** Patients shall participate in care planning.
**Acceptance Criteria:** Patient-facing care plan view | Goal setting with patient | Patient portal access to care plans

## Total Requirements: 14

**End of Care Coordination Requirements**
