# Patient Portal

**Capability ID:** 09
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Patient Portal provides patients secure web-based access to their medical records, lab results, medications, appointments, secure messaging with providers, and online bill payment enabling patient engagement and supporting regulatory requirements for patient access.

**Primary Purpose:** Empower patients with access to their health information, facilitate patient-provider communication, enable appointment management, and support patient self-service reducing administrative burden.

**Primary Users:** Patients, patient family members, caregivers (with authorized access)

---

## Use Cases

1. **View Medical Records** - Access demographics, problems, medications, allergies, immunizations
2. **View Lab Results** - Review test results and reports
3. **Secure Messaging** - Communicate with healthcare team
4. **Appointment Requests** - Request or schedule appointments online
5. **Medication Review** - View current and past medications
6. **Document Access** - View and download medical documents
7. **Bill Payment** - Pay medical bills online
8. **Health Questionnaires** - Complete intake forms and assessments
9. **Proxy Access** - Parent/caregiver access for dependents
10. **Data Download** - Export personal health records

---

## Key Features

### Medical Record Access
- **Demographics:** View and update contact information
- **Medical History:** Past medical history, surgical history, family history
- **Problem List:** Active and resolved medical conditions
- **Medications:** Current medication list with dosages
- **Allergies:** Documented allergies and reactions
- **Immunizations:** Vaccination history with dates
- **Vital Signs:** Blood pressure, weight, BMI trends
- **Procedures:** Procedures performed

**Permissions:** Read-only for clinical data; editable for some demographic fields
**Files:** `/portal/patient/get_*.php` (various endpoints for data retrieval)

### Lab Results
- View completed lab results
- Abnormal value highlighting
- Reference ranges displayed
- Historical result trending
- Downloadable reports
- Provider comments/interpretation

**Timing:** Results released after provider review (configurable delay)
**Files:** `/portal/patient/get_lab_results.php`

### Secure Messaging
- Send messages to healthcare providers
- Receive responses from care team
- Attach files/images
- Message threads organized by topic
- HIPAA-compliant secure messaging
- Read receipts
- Priority marking

**Files:** `/portal/messaging/`
**Security:** Encrypted transmission, secure authentication required

### Appointment Management
- View upcoming appointments
- View appointment history
- Request new appointments (specific date/time or general request)
- Cancel/reschedule appointments (if enabled)
- Appointment reminders via email/SMS

**Integration:** Links to scheduling system
**Files:** `/portal/find_appt_popup_user.php`, `/portal/add_edit_event_user.php`

### Document Access
- View uploaded medical documents
- Download documents (PDFs, images)
- Document categories (lab reports, imaging, visit summaries, etc.)
- CCDA download for personal health record portability

**Files:** `/portal/patient/get_patient_documents.php`
**Standards:** CCDA export for continuity of care

### Online Bill Payment
- View account balance
- View itemized statements
- Pay via credit card or ACH
- Payment history
- Receipt generation
- Automated payment processing

**Payment Processors:** Stripe, Authorize.net, Square (configurable)
**Files:** `/portal/portal_payment.php`
**Security:** PCI-compliant payment processing

### Health Questionnaires
- Complete intake forms before visits
- Health risk assessments
- PHQ-9, GAD-7 screening tools
- FHIR Questionnaire support
- Responses automatically imported to chart

**Files:** `/portal/questionnaire_render.php`
**Standards:** FHIR Questionnaire/QuestionnaireResponse

### Account Management
- Self-registration (if enabled)
- Username/password management
- Password reset via email
- Multi-factor authentication (optional)
- Session timeout for security
- Activity log

**Files:** `/portal/account/`, `/portal/index.php`
**Security:** Bcrypt password hashing, session management, CSRF protection

---

## Configuration

**Global Settings:**
- **Portal Enable** - Turn portal on/off
- **Self-Registration** - Allow patient self-sign-up
- **Result Release Delay** - Hours after provider review before patient sees results
- **Messaging** - Enable secure messaging
- **Appointments** - Allow appointment requests
- **Documents** - Which categories accessible
- **Bill Payment** - Enable online payments, payment processor
- **MFA** - Require multi-factor authentication
- **Session Timeout** - Inactivity timeout (minutes)
- **Portal URL** - Custom URL for patient access

**ACL Requirements:**
- Portal access configured per-patient (enabled/disabled)
- Data access governed by portal-specific permissions

---

## Related Capabilities

- [Patient Management](01-patient-management.md) - Portal account creation
- [Clinical Documentation](02-clinical-documentation.md) - Data displayed in portal
- [Laboratory Management](05-laboratory-management.md) - Lab results access
- [Appointment Scheduling](03-appointment-scheduling.md) - Appointment requests
- [Billing](04-billing-revenue-cycle.md) - Bill payment
- [Messaging](11-messaging-communication.md) - Secure messaging backend

---

## Compliance and Standards

- **21st Century Cures Act** - Patient access to health information
- **ONC Certification** - Patient portal requirements
- **HIPAA** - Secure access controls, audit logging
- **FHIR** - Patient API access (parallel to portal)
- **PCI-DSS** - Payment card security (if accepting cards)
- **Meaningful Use** - Patient engagement measures

---

**End of Patient Portal Capability**
