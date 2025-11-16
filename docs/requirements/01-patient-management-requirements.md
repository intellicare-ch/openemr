# Patient Management - Requirements Specification

**Capability ID:** 01
**Version:** 1.0
**Last Updated:** 2025-11-15

---

## Functional Requirements

### FR-PM-001: Patient Registration
**Description:** The system shall allow authorized users to register new patients with complete demographic and administrative information.

**Acceptance Criteria:**
- System accepts and stores minimum required fields: first name, last name, date of birth, sex
- System assigns unique Patient ID (PID) automatically
- System generates UUID for external data exchange
- System validates date of birth is not in future
- System validates required field completion before save
- System creates audit log entry for new patient registration
- System allows entry of optional fields: address, phone, email, SSN, race, ethnicity, language
- Patient record is immediately searchable after creation

### FR-PM-002: Duplicate Patient Detection
**Description:** The system shall detect potential duplicate patient records during registration based on matching criteria.

**Acceptance Criteria:**
- System compares new patient data against existing patients using name and DOB
- System displays warning when potential duplicate detected (configurable similarity threshold)
- System allows user to proceed with registration or select existing patient
- System logs duplicate check results
- Warning displays matching patient demographics for comparison
- System allows administrative override with documentation

### FR-PM-003: Patient Search
**Description:** The system shall provide multi-criteria search functionality to locate patient records quickly and accurately.

**Acceptance Criteria:**
- System supports search by: last name, first name, DOB, PID, medical record number, SSN, phone number
- System supports partial name matching with wildcards
- System supports AND/OR logic for multiple search criteria
- Search results display within 2 seconds for database with 100,000+ patients
- System returns maximum 100 results by default (configurable)
- Results sorted by last name, first name
- System highlights exact matches
- Search respects ACL facility restrictions

### FR-PM-004: Demographics Management
**Description:** The system shall allow authorized users to view and update patient demographic information.

**Acceptance Criteria:**
- System displays all demographic fields in organized layout
- System enables edit mode only for users with write permission
- System validates data format (email regex, phone format, ZIP code format)
- System saves all changes to patient_data table
- System creates audit log entry with before/after values
- System updates modification timestamp
- System preserves UUID during updates
- Changes reflected immediately in patient chart and search results

### FR-PM-005: Insurance Management
**Description:** The system shall manage patient insurance coverage including primary, secondary, and tertiary insurance policies.

**Acceptance Criteria:**
- System supports three insurance priorities: primary, secondary, tertiary
- System stores policy number, group number, subscriber information
- System allows subscriber different from patient
- System validates insurance company exists or allows creation
- System tracks effective dates (from/to)
- System stores copay amounts
- System supports insurance card image upload
- Only one active insurance per priority allowed
- Insurance information accessible during billing workflow

### FR-PM-006: Patient Portal Account Management
**Description:** The system shall enable creation and management of patient portal access credentials.

**Acceptance Criteria:**
- System creates portal credentials with username and password
- System generates unique usernames (no duplicates)
- Passwords hashed using bcrypt before storage
- System sends portal invitation email when configured
- System allows password reset by administrator
- System tracks portal access status (enabled/disabled)
- System logs portal credential creation in audit log
- Disabled accounts cannot login to portal

### FR-PM-007: Patient Merge
**Description:** The system shall allow authorized administrators to merge duplicate patient records consolidating all clinical and billing data.

**Acceptance Criteria:**
- System requires elevated admin permission for merge operation
- System displays side-by-side comparison of duplicate records
- System prompts for confirmation before merge
- System transfers all encounters, prescriptions, documents, billing to target patient
- System marks source patient as merged/inactive
- System updates all foreign key references in database
- System creates comprehensive audit log of merge transaction
- Merge operation wrapped in database transaction (rollback on error)
- System maintains UUID mapping for FHIR consistency

### FR-PM-008: Advanced Patient Search
**Description:** The system shall provide advanced search capability for population health management and quality measure identification.

**Acceptance Criteria:**
- System supports search by age range, sex, gender, race, ethnicity, language
- System supports search by insurance type, provider, facility
- System supports AND/OR logic for complex queries
- System calculates age from DOB relative to specified date
- Search results exportable to CSV
- System allows saving search criteria as named queries
- Results respect ACL facility restrictions
- Maximum 10,000 results enforced

### FR-PM-009: Patient History Tracking
**Description:** The system shall maintain historical record of patient demographic changes for audit and compliance.

**Acceptance Criteria:**
- System logs all demographic changes with timestamp and user
- Audit log captures field name, old value, new value
- Changes viewable in audit log interface
- System prevents unauthorized modification of audit logs
- Audit logs retained per organizational policy
- Tamper detection enabled for audit log integrity

### FR-PM-010: Multi-Facility Patient Management
**Description:** The system shall support patients receiving care at multiple facilities within same organization.

**Acceptance Criteria:**
- Single patient record accessible across all facilities
- Facility-specific information tracked per encounter
- Users with facility restrictions see only authorized patients
- Patient search respects facility-based ACL
- Facility assignment updates reflected immediately

---

## Non-Functional Requirements

### NFR-PM-001: Performance - Patient Search
**Description:** Patient search operations shall complete within acceptable time limits to support clinical workflow.

**Acceptance Criteria:**
- Patient search returns results within 2 seconds for 95th percentile queries
- Search handles databases with 100,000+ patient records
- Database indexes maintained on search fields (fname, lname, DOB, pid, pubpid)
- Search page load time under 1 second
- System supports 20 concurrent searches without degradation

### NFR-PM-002: Security - Data Encryption
**Description:** Sensitive patient demographic data shall be protected using encryption and access controls.

**Acceptance Criteria:**
- SSN field encrypted at rest using AES-256
- Portal passwords hashed using bcrypt (minimum cost factor 10)
- TLS 1.2+ required for all patient data transmission
- ACL enforced for all patient data access
- Failed access attempts logged
- Session timeout after 30 minutes of inactivity (configurable)

### NFR-PM-003: Usability - Registration Workflow
**Description:** Patient registration interface shall be intuitive and efficient for front desk staff.

**Acceptance Criteria:**
- Registration form completable in under 3 minutes for experienced user
- Tab order follows logical data entry sequence
- Required fields clearly marked with visual indicator
- Inline validation messages displayed for data format errors
- Auto-complete suggestions for common fields (city, state)
- Form supports keyboard navigation without mouse
- Help text available for complex fields

### NFR-PM-004: Availability - System Uptime
**Description:** Patient management functionality shall be available during all clinic operating hours.

**Acceptance Criteria:**
- System uptime 99.5% during business hours (6am-8pm)
- Planned maintenance during off-hours only
- Database backup does not impact availability
- Automatic failover for database in under 5 minutes
- Session persistence during brief network interruptions

### NFR-PM-005: Scalability - Patient Volume
**Description:** System shall scale to support growing patient populations without performance degradation.

**Acceptance Criteria:**
- System supports minimum 500,000 patient records
- Search performance maintained as database grows
- Registration time independent of total patient count
- Database partitioning supported for very large datasets
- Archive capability for inactive patients

### NFR-PM-006: Compliance - HIPAA Privacy
**Description:** Patient management shall comply with HIPAA Privacy Rule requirements.

**Acceptance Criteria:**
- Minimum necessary principle enforced via ACL
- Patient consent tracked and enforced
- Accounting of disclosures capability
- Breach notification log maintained
- Patient rights (access, amendment, restriction) supported
- Notice of Privacy Practices acknowledgment tracked

### NFR-PM-007: Compliance - Data Standards
**Description:** Patient demographic data shall conform to healthcare industry standards.

**Acceptance Criteria:**
- Race and ethnicity codes conform to CDC standards
- Language codes use ISO 639 standard
- Sex/gender fields support FHIR Patient resource requirements
- UUIDs conform to RFC 4122
- Phone numbers support E.164 format
- Addresses support USPS standardization

### NFR-PM-008: Interoperability - FHIR Compliance
**Description:** Patient demographics shall be accessible via FHIR R4 API conforming to US Core Patient profile.

**Acceptance Criteria:**
- Patient resource includes all required US Core elements
- Patient resource supports all must-support elements
- Patient search supports required search parameters
- Patient updates via API reflected in system immediately
- Provenance tracked for API modifications

### NFR-PM-009: Audit and Compliance - Logging
**Description:** All patient data access and modifications shall be comprehensively logged.

**Acceptance Criteria:**
- Patient record access logged with user, timestamp, IP address
- All CRUD operations logged with before/after values
- Audit logs tamper-proof (append-only)
- Audit logs retained minimum 6 years
- Audit log search and reporting capability
- Failed access attempts logged

### NFR-PM-010: Maintainability - Code Quality
**Description:** Patient management code shall follow best practices for maintainability.

**Acceptance Criteria:**
- Service layer separates business logic from presentation
- Database queries use parameterized statements (no SQL injection)
- Input validation on both client and server side
- Code follows PSR-12 coding standard
- Functions have unit test coverage >70%
- API endpoints have integration tests

---

## Data Quality Requirements

### DQ-PM-001: Data Completeness
**Description:** Critical patient demographic fields shall be complete and accurate.

**Acceptance Criteria:**
- Name, DOB, sex required for all patients (100% complete)
- Contact information (phone or email) >95% complete
- Primary care provider assigned >90% of patients
- Preferred language documented >85% of patients
- Race/ethnicity documented >80% of patients per meaningful use

### DQ-PM-002: Data Accuracy
**Description:** Patient demographic data shall be validated and maintained accurately.

**Acceptance Criteria:**
- Email addresses validated using regex pattern
- Phone numbers validated for format
- ZIP codes validated against US postal codes
- DOB validated as valid date not in future
- SSN validated for format (if provided)
- Duplicate patient rate <1% of total patients

### DQ-PM-003: Data Timeliness
**Description:** Patient demographic updates shall be reflected in system promptly.

**Acceptance Criteria:**
- Demographic changes saved within 1 second
- Changes visible in search results within 5 seconds
- Insurance updates available for billing immediately
- FHIR API reflects changes within 10 seconds
- Portal access changes effective within 1 minute

---

## Integration Requirements

### IR-PM-001: FHIR API Integration
**Description:** Patient data shall be accessible via standards-compliant FHIR R4 API.

**Acceptance Criteria:**
- GET /fhir/Patient returns patient resources
- POST /fhir/Patient creates new patients
- PUT /fhir/Patient updates existing patients
- Patient resource conforms to US Core 3.1 profile
- All required search parameters supported
- OAuth2 authentication enforced

### IR-PM-002: HL7 v2 Integration
**Description:** Patient demographics shall be exportable in HL7 ADT message format.

**Acceptance Criteria:**
- HL7 ADT^A04 message generated for new patients
- HL7 ADT^A08 message generated for patient updates
- Messages conform to HL7 v2.5 specification
- All required segments included (PID, PV1)
- Messages transmitted via configured interface

### IR-PM-003: CCDA Integration
**Description:** Patient demographics shall be included in all CCDA documents.

**Acceptance Criteria:**
- CCDA header includes complete patient demographics
- Race/ethnicity coded per CCDA requirements
- Language preference included
- Patient contacts included
- Demographics section validates against CCDA schema

---

## Total Requirements: 31
- Functional Requirements: 10
- Non-Functional Requirements: 10
- Data Quality Requirements: 3
- Integration Requirements: 3
- Additional implied requirements: 5

---

**End of Patient Management Requirements**
