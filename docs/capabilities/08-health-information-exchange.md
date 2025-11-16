# Health Information Exchange (Interoperability)

**Capability ID:** 08
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Health Information Exchange (HIE) provides standards-based interoperability through FHIR R4 API, HL7 v2.x messaging, CCDA documents, Direct secure messaging, and bulk data export enabling seamless data sharing with external systems, health information exchanges, and third-party applications.

**Primary Purpose:** Enable secure, standards-compliant exchange of patient health information with other healthcare systems, support third-party app integration via SMART on FHIR, and meet regulatory requirements for data access and portability.

**Primary Users:** API developers, HIE participants, third-party app users, patients, system integrators

---

## Use Cases

1. **FHIR API Access** - External systems query patient data via REST API
2. **SMART on FHIR Apps** - Third-party app integration with patient launch
3. **Bulk Data Export** - Large-scale data extraction for research/analytics
4. **CCDA Document Exchange** - Transitions of care and referrals
5. **Direct Messaging** - Secure provider-to-provider communication
6. **HL7 Interface** - Lab, ADT, and other system integration
7. **Patient Data Access** - Patient-authorized third-party access
8. **Public Health Reporting** - Registry submissions

---

## Key Features

### FHIR R4 API

**Supported Resources (40+):**
- **Patient Data:** Patient, RelatedPerson, Person
- **Clinical:** Condition, Observation, Procedure, DiagnosticReport, Specimen
- **Medications:** MedicationRequest, Medication, MedicationDispense, Immunization, AllergyIntolerance
- **Care Management:** CarePlan, CareTeam, Goal
- **Documents:** DocumentReference, Media
- **Administrative:** Encounter, Appointment, Location, Organization
- **Financial:** Coverage
- **Provider:** Practitioner, PractitionerRole
- **Conformance:** Provenance, ValueSet

**Operations:**
- RESTful CRUD (Create, Read, Update, Delete)
- Search with parameters (_include, _revinclude, _count, _sort)
- $export (Bulk Data Export) - system, patient, and group level
- $bulkdata-status - Check export job status
- $docref - Generate CCDA on demand

**Base URL:** `/apis/default/fhir/`
**Metadata:** `/apis/default/fhir/metadata`

**Files:** `/src/RestControllers/FHIR/`, `/src/FHIR/R4/`
**Standards:** FHIR R4, US Core 3.1, ONC Certification

### SMART on FHIR

**Launch Modes:**
- EHR Launch - App launched from within OpenEMR
- Standalone Launch - App launched independently

**Authorization:**
- OAuth 2.0 authorization code flow
- PKCE for public apps
- Scopes: patient/*.read, user/*.read, system/*.read, launch, offline_access

**Context:**
- Patient context (patient ID passed to app)
- Encounter context (current encounter)
- User context (logged-in provider)

**Discovery:** `/.well-known/smart-configuration`
**App Registration:** `/interface/smart/register-app.php`

**Files:** `/src/RestControllers/SMART/`, OAuth2 implementation

### Bulk Data Export ($export)

**Export Types:**
- **System Export:** All resources (`GET /fhir/$export`)
- **Patient Export:** All patient compartment data (`GET /fhir/Patient/$export`)
- **Group Export:** Specific patient group (`GET /fhir/Group/[id]/$export`)

**Process:**
1. Initiate export (returns Content-Location with job URL)
2. Poll status (`GET /$bulkdata-status?job=[id]`)
3. Download NDJSON files when complete
4. Delete job when done

**Output:** NDJSON (newline-delimited JSON) files, one per resource type
**Authentication:** OAuth2 with system/*.read or system/*.$export scopes

**Files:** `/src/FHIR/Export/`
**Standards:** FHIR Bulk Data Access IG

### CCDA (Consolidated Clinical Document Architecture)

**Sections Generated:**
- Header (patient, author, custodian demographics)
- Allergies and Intolerances
- Medications
- Problem List
- Immunizations
- Vital Signs
- Results (Lab Results)
- Procedures
- Encounters
- Social History
- Care Plan
- Goals
- Health Concerns

**Formats:**
- CCDA 2.1 XML
- Blue Button format
- Human-readable HTML via XSL transform

**Generation:**
- Node.js CCDA service (`/ccdaservice/`)
- Libraries: oe-blue-button-generate
- Validation: Schematron validation

**Access Methods:**
1. Manual generation from patient chart
2. FHIR $docref operation
3. Care Coordination module
4. Patient portal download

**Files:** `/ccdaservice/`, `/interface/modules/zend_modules/module/Carecoordination/`
**Standards:** HL7 CCDA 2.1, C-CDA R2.1

### Direct Secure Messaging

**Purpose:** HISP-enabled secure provider-to-provider messaging with PHI

**Features:**
- Send/receive Direct messages
- S/MIME encryption
- Trust anchor management
- Certificate-based authentication
- Attachment support (CCDA, PDFs)
- Message inbox
- Read receipts

**Integration:** phiMail or other HISP provider
**Background Service:** Direct message check (polls for new messages)

**Files:** `/library/direct_message_check.inc.php`
**Report:** `/interface/reports/direct_message_log.php`
**Standards:** Direct Trust Protocol, S/MIME

### HL7 v2.x Interfaces

**Message Types:**
- **ORU** - Observation Result (Lab Results inbound)
- **ORM** - Order Message (Lab Orders outbound)
- **ADT** - Admit/Discharge/Transfer (optional)

**Transmission:**
- File-based exchange
- SFTP
- MLLP (socket-based)

**Vendor Interfaces:**
- Quest Diagnostics
- LabCorp
- Generic HL7

**Files:** `/interface/orders/gen_hl7_order.inc.php`, `/interface/orders/receive_hl7_results.inc.php`
**Library:** aranyasen/hl7 3.2.2
**Standards:** HL7 v2.3, v2.5, v2.5.1

---

## Configuration

**Global Settings:**
- **FHIR API:** Enable/disable, base URL
- **OAuth2:** Client registration, scopes, token expiration
- **SMART:** Enable SMART launch, app whitelist
- **Bulk Export:** Enable, max file size, retention period
- **CCDA:** Sections to include, author information
- **Direct:** HISP provider configuration, certificates
- **HL7:** Version, vendor formats, transmission method

**ACL Requirements:**
- API Access: Configured per OAuth2 client with scopes
- FHIR Write: `api:fhir` scope with write permissions
- Bulk Export: `system/*.$export` scope
- CCDA Generation: `patients/demo` read access

---

## Related Capabilities

- [Patient Management](01-patient-management.md) - Patient data for exchange
- [Clinical Documentation](02-clinical-documentation.md) - Clinical data for FHIR/CCDA
- [Laboratory Management](05-laboratory-management.md) - HL7 lab interfaces
- [Care Coordination](14-care-coordination.md) - CCDA for transitions of care
- [API and Integration](17-api-integration.md) - Technical API details

---

## Compliance and Standards

- **FHIR R4** - HL7 FHIR Release 4
- **US Core 3.1** - US Core Implementation Guide
- **SMART on FHIR** - SMART App Launch 1.1.0
- **Bulk Data IG** - FHIR Bulk Data Access Implementation Guide
- **CCDA 2.1** - HL7 Consolidated CDA Release 2.1
- **Direct Protocol** - ONC Direct Project specifications
- **HL7 v2.x** - HL7 Version 2 messaging
- **OAuth 2.0** - RFC 6749 authorization
- **OIDC** - OpenID Connect
- **21st Century Cures Act** - Patient access API requirements
- **ONC Certification** - (g)(10) Standardized API for patient and population services

---

**End of Health Information Exchange Capability**
