# Patient Portal - Requirements Specification

**Capability ID:** 09
**Version:** 1.0
**Last Updated:** 2025-11-15

## Functional Requirements

### FR-POR-001: Patient Account Registration
**Description:** Patients shall create portal accounts with secure credentials.
**Acceptance Criteria:**
- Self-registration supported (if enabled)
- Email verification required
- Password strength requirements enforced
- Unique username required
- Privacy policy acceptance documented

### FR-POR-002: Medical Record Access
**Description:** Patients shall view their medical records.
**Acceptance Criteria:**
- Demographics viewable and editable (contact info)
- Problem list viewable
- Medication list viewable
- Allergy list viewable
- Immunization history viewable
- Vital signs viewable
- Procedure history viewable

### FR-POR-003: Lab Results Access
**Description:** Patients shall view laboratory results.
**Acceptance Criteria:**
- Results released after provider review (configurable delay)
- Result value, reference range, and interpretation displayed
- Abnormal values highlighted
- Historical results viewable
- Result PDFs downloadable
- Provider comments visible

### FR-POR-004: Secure Messaging
**Description:** Patients shall communicate securely with providers.
**Acceptance Criteria:**
- Send messages to care team
- Receive responses from providers
- Message threading/conversation view
- Attach files/images
- Read receipts
- HIPAA-compliant encryption

### FR-POR-005: Appointment Management
**Description:** Patients shall manage appointments via portal.
**Acceptance Criteria:**
- View upcoming appointments
- View appointment history
- Request new appointments
- Cancel appointments (if enabled)
- Reschedule appointments (if enabled)
- Receive appointment reminders

### FR-POR-006: Document Access
**Description:** Patients shall access medical documents.
**Acceptance Criteria:**
- View uploaded documents
- Download documents (PDFs, images)
- Document categories (lab reports, imaging, visit summaries)
- CCDA download for portability

### FR-POR-007: Bill Payment
**Description:** Patients shall pay bills online.
**Acceptance Criteria:**
- View account balance
- View itemized statements
- Pay via credit card or ACH
- Payment history viewable
- Receipts generated and emailable
- PCI-compliant payment processing

### FR-POR-008: Health Questionnaires
**Description:** Patients shall complete health forms.
**Acceptance Criteria:**
- Intake forms before visits
- Health risk assessments
- Screening tools (PHQ-9, GAD-7)
- Responses auto-imported to chart
- Save partial completion and resume

### FR-POR-009: Password Management
**Description:** Patients shall manage portal access.
**Acceptance Criteria:**
- Password reset via email
- Change password while logged in
- Security questions (optional)
- Account lockout after failed attempts
- Session timeout after inactivity

### FR-POR-010: Proxy Access
**Description:** Parents/caregivers shall access dependent records.
**Acceptance Criteria:**
- Proxy relationship establishable
- Multiple proxy types (parent, guardian, caregiver)
- Access permissions configurable
- Age-based automatic expiration (e.g., at age 18)
- Proxy access logged

## Non-Functional Requirements

### NFR-POR-001: Performance
**Description:** Portal shall be responsive.
**Acceptance Criteria:**
- Page load time < 2 seconds
- Dashboard displays within 3 seconds
- Search results within 2 seconds
- Mobile-responsive design

### NFR-POR-002: Security
**Description:** Portal shall protect patient data.
**Acceptance Criteria:**
- Bcrypt password hashing
- TLS 1.2+ required
- Multi-factor authentication (optional)
- Session timeout after 30 minutes
- Brute force protection (account lockout)
- Password history prevents reuse

### NFR-POR-003: Availability
**Description:** Portal shall be available 24/7.
**Acceptance Criteria:**
- 99.5% uptime
- Maintenance during off-peak hours only
- Planned downtime announced in advance
- Graceful degradation if backend unavailable

### NFR-POR-004: Usability
**Description:** Portal shall be user-friendly.
**Acceptance Criteria:**
- Intuitive navigation
- Mobile-friendly (responsive design)
- Accessibility (WCAG 2.1 Level AA)
- Plain language (health literacy)
- Help text and tutorials

### NFR-POR-005: Compliance - HIPAA
**Description:** Portal shall comply with HIPAA.
**Acceptance Criteria:**
- Access controls enforced
- Audit trail of patient data access
- Breach notification capability
- Patient rights supported (access, amendment)

### NFR-POR-006: Compliance - ONC
**Description:** Portal shall meet ONC patient access requirements.
**Acceptance Criteria:**
- View, download, and transmit (VDT) capabilities
- No fees for portal access
- Access without special effort
- Timely access (within 1 business day)

### NFR-POR-007: Privacy
**Description:** Portal communications shall be private.
**Acceptance Criteria:**
- Messages encrypted in transit and at rest
- Provider messages not visible to other staff without permission
- Patient controls who can proxy access
- Opt-out of reminders and communications

### NFR-POR-008: Auditability
**Description:** Portal activity shall be logged.
**Acceptance Criteria:**
- Login attempts logged
- Data access logged
- Document downloads logged
- Message sends/receives logged
- Logs retained minimum 6 years

### NFR-POR-009: Localization
**Description:** Portal shall support multiple languages.
**Acceptance Criteria:**
- Interface translatable to patient's preferred language
- Content in common languages (Spanish, etc.)
- Right-to-left language support

### NFR-POR-010: Reliability
**Description:** Portal shall protect against data loss.
**Acceptance Criteria:**
- Form auto-save prevents data loss
- Message sends confirmed
- Backup and recovery for portal data
- Transaction integrity

## Data Quality Requirements

### DQ-POR-001: Data Timeliness
**Description:** Portal data shall be current.
**Acceptance Criteria:**
- Lab results released within configured delay (e.g., 24 hours)
- Demographic changes reflected immediately
- New appointments visible within 5 minutes
- Messages delivered within 1 minute

### DQ-POR-002: Data Accuracy
**Description:** Portal data shall match EHR.
**Acceptance Criteria:**
- Medication list matches EHR
- Allergy list matches EHR
- Lab results match EHR exactly
- Appointments match schedule

### DQ-POR-003: Contact Information Currency
**Description:** Patient contact information shall be current.
**Acceptance Criteria:**
- Email address validation on entry
- Phone number validation
- Address validation
- Bounce notification for invalid emails

## Integration Requirements

### IR-POR-001: EHR Integration
**Description:** Portal shall integrate with EHR in real-time.
**Acceptance Criteria:**
- Portal reads current EHR data
- Portal writes (appointment requests, messages) sync to EHR immediately
- No data duplication
- Referential integrity maintained

### IR-POR-002: Payment Gateway Integration
**Description:** Payments shall integrate with payment processor.
**Acceptance Criteria:**
- Stripe or Authorize.net integration
- PCI compliance (no card storage)
- Payment confirmation immediate
- Failed payments handled gracefully

### IR-POR-003: Messaging Integration
**Description:** Portal messages shall integrate with provider workflow.
**Acceptance Criteria:**
- Provider inbox receives portal messages
- Provider responses delivered to portal
- Notification to provider when message received
- Message routing to appropriate care team member

### IR-POR-004: Third-Party App Access
**Description:** Patients shall authorize third-party apps.
**Acceptance Criteria:**
- OAuth2 consent workflow
- Patient controls app permissions
- App access revocable by patient
- Access log viewable by patient

**Total Requirements: 34**

**End of Patient Portal Requirements**
