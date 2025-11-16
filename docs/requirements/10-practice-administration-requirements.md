# Practice Administration - Requirements Specification
**Capability ID:** 10 | **Version:** 1.0 | **Last Updated:** 2025-11-15

## Functional Requirements
### FR-PA-001: User Management
**Description:** System shall manage user accounts with roles and permissions.
**Acceptance Criteria:** Create/edit/delete users | Assign ACL groups | Configure providers with NPI | MFA setup | User activation/deactivation | Password policies enforced | Audit log for user changes

### FR-PA-002: Access Control List (ACL)
**Description:** System shall enforce role-based permissions.
**Acceptance Criteria:** Granular permissions (admin, patients, encounters, billing) | Group-based assignment | Permission inheritance | Object-level security (ARO/ACO/AXO) | Real-time enforcement | ACL changes immediate

### FR-PA-003: Facility Management
**Description:** System shall manage multiple facilities.
**Acceptance Criteria:** Facility demographics (name, address, NPI, tax ID) | Billing settings per facility | Assign users to facilities | Facility hours configurable | Multi-facility support | POS codes per facility

### FR-PA-004: Global Settings
**Description:** System shall provide centralized configuration.
**Acceptance Criteria:** 180+ settings across categories | Appearance, locale, features, security | Settings organized in tabs | Changes effective immediately or after logout | Import/export configurations

### FR-PA-005: Code System Management
**Description:** System shall maintain medical code sets.
**Acceptance Criteria:** ICD-10, SNOMED, RxNorm, CPT, LOINC, CVX imports | Code search functionality | Fee schedules per CPT | Custom code lists | Code activation/deactivation | Annual updates

## Non-Functional Requirements
### NFR-PA-001: Security - Admin Access
**Description:** Administrative functions shall be restricted.
**Acceptance Criteria:** Admin-level ACL required | Admin actions logged | Sensitive settings require confirmation | Session timeout for admin users | Multi-factor auth for admins (optional)

### NFR-PA-002: Auditability
**Description:** Admin changes shall be logged.
**Acceptance Criteria:** User changes logged | ACL changes logged | Global setting changes logged | Code system updates logged | Logs retained 6+ years

### NFR-PA-003: Usability
**Description:** Admin interfaces shall be efficient.
**Acceptance Criteria:** User creation < 2 minutes | Settings searchable | Bulk operations for users | Configuration wizards for complex setup

## Total Requirements: 18

**End of Practice Administration Requirements**
