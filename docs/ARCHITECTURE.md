# OpenEMR High-Level Architecture Documentation

**Version:** 7.0.4+
**Last Updated:** 2025-11-13
**Classification:** Technical Architecture Overview

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [System Overview](#system-overview)
3. [Architectural Principles](#architectural-principles)
4. [Technology Stack](#technology-stack)
5. [System Architecture](#system-architecture)
6. [Component Architecture](#component-architecture)
7. [Data Architecture](#data-architecture)
8. [API Architecture](#api-architecture)
9. [Security Architecture](#security-architecture)
10. [Integration Architecture](#integration-architecture)
11. [Deployment Architecture](#deployment-architecture)
12. [Extensibility Architecture](#extensibility-architecture)
13. [Performance and Scalability](#performance-and-scalability)
14. [Compliance and Standards](#compliance-and-standards)

---

## Executive Summary

OpenEMR is a free and open-source Electronic Health Records (EHR) and Medical Practice Management application designed for ambulatory care settings. The system provides comprehensive patient management, clinical documentation, scheduling, billing, and practice management capabilities while maintaining compliance with healthcare standards including HIPAA, HL7, FHIR R4, and ONC Certification requirements.

### Key Architectural Characteristics

- **Platform:** LAMP stack (Linux, Apache/Nginx, MySQL/MariaDB, PHP 8.2+)
- **Architecture Style:** Layered monolith with service-oriented components
- **Deployment Model:** Self-hosted, multi-tenant capable
- **API Support:** RESTful APIs, FHIR R4, HL7 v2.x, CCDA
- **Security Model:** Role-based access control (RBAC) with granular ACL
- **Database:** Relational (MySQL/MariaDB) with 281+ tables
- **Internationalization:** 70+ languages supported
- **Certification:** ONC 2015 Edition Certified

---

## System Overview

### Purpose

OpenEMR serves as a complete practice management and electronic health records system for healthcare providers, supporting:

- **Clinical Care Delivery:** Patient encounters, documentation, orders, results
- **Practice Management:** Scheduling, facility management, user administration
- **Revenue Cycle Management:** Billing, claims, insurance, payments
- **Health Information Exchange:** FHIR APIs, Direct messaging, CCDA documents
- **Patient Engagement:** Patient portal, secure messaging, online payments
- **Regulatory Compliance:** Quality measures (CQM), meaningful use (AMC), audit logging

### Target Users

- **Primary Care Physicians** - Family medicine, internal medicine
- **Specialists** - Various specialty practices
- **Behavioral Health** - Mental health and substance abuse treatment
- **Urgent Care** - Walk-in clinics
- **Rural Health Clinics** - Federally qualified health centers
- **International Healthcare Organizations** - Multi-language support

### System Capabilities (High-Level)

1. Patient Management
2. Clinical Documentation
3. Appointment Scheduling
4. Billing and Revenue Cycle
5. Laboratory Management
6. Pharmacy and Prescriptions
7. Reporting and Analytics
8. Health Information Exchange
9. Patient Portal
10. Practice Administration

---

## Architectural Principles

### Design Principles

1. **Open Standards Compliance**
   - Adherence to FHIR, HL7, CCDA, X12 EDI standards
   - Healthcare interoperability first

2. **Security by Design**
   - HIPAA compliance built-in
   - Defense-in-depth security model
   - Comprehensive audit logging

3. **Extensibility**
   - Module system for custom functionality
   - Event-driven architecture for hooks
   - Custom form builder
   - API-first for integrations

4. **Multi-tenancy**
   - Single codebase supporting multiple sites
   - Isolated data per tenant
   - Site-specific configurations

5. **Internationalization**
   - Language-agnostic design
   - Cultural and regulatory adaptability
   - Unicode support

6. **Backward Compatibility**
   - Smooth upgrade paths
   - Legacy code coexistence with modern implementations
   - Database migration framework

### Architectural Patterns

- **Layered Architecture:** Presentation → Business Logic → Data Access
- **Service Layer Pattern:** Encapsulation of business logic in service classes
- **Repository Pattern:** Data access abstraction (implicit in services)
- **MVC Pattern:** Model-View-Controller for modern components
- **Event-Driven Pattern:** Symfony EventDispatcher for loose coupling
- **Template Pattern:** Smarty and Twig for view separation
- **Factory Pattern:** Object creation in controllers and services
- **Singleton Pattern:** Database connections, configuration

---

## Technology Stack

### Backend Technologies

#### Core Platform
- **PHP:** 8.2+ (minimum requirement)
- **Web Server:** Apache 2.4+ or Nginx 1.14+
- **Database:** MySQL 5.7.8+ or MariaDB 10.2.2+
- **Node.js:** 14+ (for CCDA service)

#### PHP Frameworks & Libraries
| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| MVC Framework | Laminas | 3.8.0 | Model-View-Controller architecture |
| HTTP Foundation | Symfony | 6.4.26+ | Request/Response handling |
| Event Dispatcher | Symfony | 6.4+ | Event-driven architecture |
| Console | Symfony | 6.4+ | CLI commands |
| OAuth2 Server | League/OAuth2 | 8.4+ | API authentication |
| Template Engine | Smarty | 4.5.6 | Legacy templating |
| Template Engine | Twig | 3.22.0 | Modern templating |
| Logger | Monolog | 3.9.0 | Application logging |
| HTTP Client | Guzzle | 7.10.0 | External API calls |
| PDF Generation | DomPDF | 3.1.4 | PDF documents |
| PDF Generation | mPDF | 8.2.6 | Alternative PDF engine |
| Excel | PHPSpreadsheet | 5.2.0 | Excel/CSV generation |
| Email | PHPMailer | 6.12.0 | Email sending |
| Database Abstraction | ADOdb | 5.22.10 | Legacy DB layer |
| HL7 Parser | aranyasen/hl7 | 3.2.2 | HL7 v2 messaging |
| JWT Tokens | lcobucci/jwt | 4.2.1 | JSON Web Tokens |
| UUID | ramsey/uuid | 4.9.1 | UUID generation |
| Environment Config | phpdotenv | 5.6.2 | .env file support |
| 2FA | robthree/twofactorauth | 1.9.4 | TOTP authentication |

### Frontend Technologies

#### Core Libraries
| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| JavaScript Library | jQuery | 3.7.1 | DOM manipulation |
| UI Framework | Bootstrap | 4.6.2 | Responsive design |
| SPA Framework | AngularJS | 1.8.3 | Specific modules |
| Data Tables | DataTables | 1.13.11 | Grid component |
| Charts | Chart.js | 4.5.1 | Data visualization |
| Select Boxes | Select2 | 4.0.13 | Enhanced dropdowns |
| Date/Time | Moment.js | 2.30.1 | Date manipulation |
| i18n | i18next | 24.2.3 | Internationalization |
| Rich Text Editor | CKEditor5 | 46.1.1 | WYSIWYG editing |
| Font Icons | Font Awesome | 6.6.0 | Icon library |

#### Build Tools
- **Task Runner:** Gulp
- **CSS Preprocessor:** SASS/SCSS
- **CSS Post-processor:** PostCSS with Autoprefixer
- **Package Manager:** npm

### Database Technologies

- **RDBMS:** MySQL 5.7+ / MariaDB 10.2+
- **Storage Engine:** InnoDB (ACID compliance, foreign keys)
- **Features Used:**
  - Transactions
  - Stored procedures
  - Views
  - Triggers
  - Full-text search indexes
- **Caching:** Redis support via predis library

### Communication Protocols

#### Web Protocols
- HTTP/HTTPS (TLS 1.2+)
- REST
- SOAP (legacy integrations)
- WebSockets (real-time features)

#### Healthcare Protocols
- **HL7 v2.x** - Clinical messaging
- **FHIR R4** - Modern healthcare API
- **CCDA** - Clinical document exchange
- **DICOM** - Medical imaging
- **X12 EDI** - Insurance transactions (270, 271, 835, 837P, 837I, 997)
- **Direct Protocol** - Secure messaging

#### Authentication Protocols
- OAuth 2.0 (RFC 6749)
- OpenID Connect (OIDC)
- PKCE (RFC 7636)
- JWT (RFC 7519)

---

## System Architecture

### Architectural Layers

```
┌─────────────────────────────────────────────────────────────┐
│                     PRESENTATION LAYER                        │
├─────────────────────────────────────────────────────────────┤
│  Web UI (jQuery, Bootstrap)  │  Patient Portal (AngularJS)  │
│  REST API                    │  FHIR API (R4)               │
│  Mobile Apps (SMART on FHIR) │  HL7 v2 Interface            │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      APPLICATION LAYER                        │
├─────────────────────────────────────────────────────────────┤
│  Controllers                  │  REST Controllers            │
│  FHIR Controllers             │  Event Dispatchers           │
│  Service Layer                │  Validators                  │
│  Business Logic               │  Workflow Engines            │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      INTEGRATION LAYER                        │
├─────────────────────────────────────────────────────────────┤
│  OAuth2 Server       │  FHIR Mapper       │  HL7 Parser     │
│  CCDA Generator      │  X12 EDI           │  Direct Msg     │
│  Lab Interfaces      │  eRx Integration   │  Clearinghouse  │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                         DATA LAYER                            │
├─────────────────────────────────────────────────────────────┤
│  MySQL/MariaDB (281 tables)  │  File Storage               │
│  Session Store               │  Document Storage            │
│  Cache (Redis)               │  Audit Logs                  │
└─────────────────────────────────────────────────────────────┘
```

### Directory Structure

```
openemr/
├── interface/              # User interface pages
│   ├── main/              # Main navigation and dashboard
│   ├── patient_file/      # Patient chart interface
│   ├── forms/             # Clinical forms (37+ types)
│   ├── billing/           # Billing and claims
│   ├── reports/           # Reporting interface (40+ reports)
│   ├── orders/            # Lab orders and results
│   └── modules/           # Module system
├── src/                   # Modern PSR-4 autoloaded classes
│   ├── Services/          # Business logic services
│   ├── RestControllers/   # REST API controllers
│   ├── FHIR/              # FHIR R4 implementation
│   ├── Events/            # Event system
│   ├── Common/            # Common utilities (ACL, Session, Logging)
│   ├── Validators/        # Input validation
│   ├── Billing/           # Billing services
│   └── RestControllers/SMART/  # SMART on FHIR
├── library/               # Legacy helper functions and classes
│   ├── classes/           # Legacy classes
│   ├── ajax/              # AJAX handlers
│   └── custom/            # Custom extensions
├── templates/             # Smarty/Twig templates
├── public/                # Public assets
│   ├── assets/            # CSS, JS, images
│   └── images/            # Image assets
├── sql/                   # Database schema and migrations
├── portal/                # Patient portal application
├── ccdaservice/           # CCDA generation service (Node.js)
├── apis/                  # API routing
├── modules/               # Installable modules
├── contrib/               # Contributed utilities (ICD, SNOMED, RxNorm)
├── gacl/                  # Access Control List library
├── vendor/                # Composer dependencies
├── node_modules/          # npm dependencies
└── sites/                 # Multi-site configurations
    └── {site_id}/         # Site-specific data
        ├── documents/     # Document storage
        ├── sqlconf.php    # DB config
        └── config.yaml    # Site config
```

---

## Component Architecture

### Core Components

#### 1. Service Layer (`/src/Services/`)

**Purpose:** Encapsulates business logic and data access

**Key Services:**
- `PatientService` - Patient CRUD and queries
- `EncounterService` - Encounter management
- `AppointmentService` - Scheduling operations
- `PrescriptionService` - Medication management
- `ImmunizationService` - Vaccination tracking
- `AllergyIntoleranceService` - Allergy management
- `ConditionService` - Problem list management
- `ProcedureService` - Procedure tracking
- `FacilityService` - Facility management
- `UserService` - User management
- `InsuranceService` - Insurance data
- `DocumentService` - Document operations

**Pattern:**
```php
class PatientService extends BaseService
{
    public function getAll($search, $isAndCondition)
    public function getOne($uuid)
    public function insert($data)
    public function update($uuid, $data)
    public function search($search, $isAndCondition)
}
```

#### 2. REST Controllers (`/src/RestControllers/`)

**Purpose:** Handle HTTP requests for standard REST API

**Responsibilities:**
- Request validation
- Authorization checking
- Service orchestration
- Response formatting
- Error handling

**Example:**
```php
class PatientRestController
{
    private $patientService;

    public function getAll($search)
    public function getOne($uuid)
    public function post($data)
    public function put($uuid, $data)
}
```

#### 3. FHIR Controllers (`/src/RestControllers/FHIR/`)

**Purpose:** FHIR R4 API implementation

**Key Controllers:**
- 40+ FHIR resource controllers
- Metadata/CapabilityStatement
- Search operations
- Include/RevInclude support
- Provenance tracking
- Bulk data export ($export)

**Architecture:**
- Resource-specific controllers inherit from `FhirResourceController`
- Uses FHIR domain resources from `/src/FHIR/R4/FHIRDomainResource/`
- Search builders for complex queries
- FHIR validators for conformance

#### 4. Event System (`/src/Events/`)

**Purpose:** Enable loose coupling and extensibility

**Event Categories:**
- Patient events (create, update, delete)
- Encounter events
- Appointment events
- Prescription events
- Document events
- User events
- Billing events

**Usage:**
```php
$dispatcher = $GLOBALS['kernel']->getEventDispatcher();
$dispatcher->dispatch(new PatientCreatedEvent($patient), PatientCreatedEvent::EVENT_NAME);
```

**Subscribers:** Modules can subscribe to events for custom logic

#### 5. Access Control (`/src/Common/Acl/`)

**Purpose:** Authorization and access control

**Components:**
- `AclMain` - Primary ACL interface
- `AclExtended` - Extended ACL features
- phpGACL integration (`/gacl/`)

**ACL Sections:**
- `admin` - Administration functions
- `acct` - Accounting/billing
- `patients` - Patient data
- `encounters` - Encounter data
- `sensitivities` - Data sensitivity levels
- Many more granular sections

**Example:**
```php
AclMain::aclCheckCore($section, $value, $user_id, $return_value)
```

#### 6. Session Management (`/src/Common/Session/`)

**Purpose:** Secure session handling

**Features:**
- Database-backed sessions (`session_tracker` table)
- Session timeout enforcement
- Concurrent session detection
- Activity tracking
- Session fixation prevention

#### 7. Audit Logging (`/src/Common/Logging/`)

**Purpose:** Comprehensive audit trail

**Components:**
- `EventAuditLogger` - Main audit logger
- `SystemLogger` - System event logging
- Integration with Monolog

**Tables:**
- `log` - Main event log
- `api_log` - API request/response logging
- `audit_master` / `audit_details` - Detailed auditing

#### 8. Validators (`/src/Validators/`)

**Purpose:** Input validation and sanitization

**Validators:**
- `ProcessingResult` - Validation result container
- `BaseValidator` - Base validation class
- Field-specific validators
- Type validators
- Constraint validators

### Supporting Components

#### Form System (`/interface/forms/`)

**Architecture:**
- 37+ built-in form types
- Common interface for form rendering
- Form versioning support
- Encounter association
- Billing integration

**Layout-Based Forms (LBF):**
- Visual form designer
- Custom field types
- Conditional display logic
- Data binding to database tables

#### Module System

**Types:**
1. **Zend Modules** (`/interface/modules/zend_modules/`)
   - Full MVC architecture
   - Laminas framework integration
   - Examples: Care Coordination, Immunization Registry

2. **Custom Modules** (`/interface/modules/custom_modules/`)
   - Simpler module structure
   - Examples: Weno eRx, Telehealth, EHI Exporter

3. **Menu Modules**
   - Add menu items and basic functionality

**Module Lifecycle:**
- Registration
- Installation
- Activation
- Execution
- Deactivation
- Uninstallation

#### Background Services

**Service Manager:** `/interface/reports/background_services.php`

**Services:**
- **phiMail** - Direct messaging check (every 5 min)
- **MedEx** - Appointment reminders
- **X12_SFTP** - Claims transmission
- **UUID_Service** - UUID population (every 4 hours)
- **Email_Service** - Email queue processing (every 2 min)

**Execution:**
- Cron-based scheduling
- Service locking
- Error handling
- Manual execution capability

---

## Data Architecture

### Database Schema

**Database Management System:** MySQL 5.7+ / MariaDB 10.2+

**Schema Statistics:**
- **Total Tables:** 281
- **Database Version:** 527 (internal versioning)
- **ACL Version:** 12
- **Storage Engine:** InnoDB (transactional)

### Key Data Entities

#### Patient Domain

**Core Table: `patient_data`**
- Patient demographics
- Contact information
- Insurance references
- Primary care provider
- Pharmacy assignment
- UUID for external exchange

**Related Tables:**
- `patient_access_onsite` - Portal access
- `patient_access_offsite` - External access
- `patient_history` - Historical changes
- `patient_birthdates` - Birth records
- `patient_tracker` - Patient flow tracking
- `patient_tracker_element` - Flow board elements

#### Clinical Domain

**Encounters: `form_encounter`**
- Visit information
- Date/time
- Facility
- Provider
- Billing facility
- POS code

**Problems: `lists` (where type='medical_problem')**
- ICD-10 diagnosis codes
- SNOMED codes
- Onset date
- Resolution date

**Medications: `lists` (where type='medication')**
- Active medications
- RxNorm codes

**Prescriptions: `prescriptions`**
- Medication orders
- Dosage instructions
- Quantity, refills
- DEA schedule
- eRx integration data

**Allergies: `lists` (where type='allergy')**
- Allergen
- Reaction
- Severity

**Immunizations: `immunizations`**
- Vaccine (CVX code)
- Administration date
- Lot number
- Manufacturer
- Administered by
- VIS (Vaccine Information Statement)

**Vitals: `form_vitals`**
- Blood pressure
- Pulse, respiration
- Temperature
- Height, weight, BMI
- Oxygen saturation

**Lab Orders: `procedure_order`**
- Order details
- Ordering provider
- Procedure provider (lab)

**Lab Results: `procedure_result`**
- Result value
- Reference range
- Abnormal flags
- LOINC codes

#### Administrative Domain

**Users: `users`**
- Credentials (bcrypt hashed)
- Name, NPI
- Specialty
- Facility assignments
- ACL group membership
- MFA settings

**Facilities: `facility`**
- Name, address
- Tax ID, NPI
- Contact information
- Billing settings

**Appointments: `openemr_postcalendar_events`**
- Date/time
- Patient
- Provider
- Facility
- Category
- Status

#### Billing Domain

**Insurance: `insurance_data`**
- Policy information
- Subscriber details
- Group number
- Priority (primary/secondary/tertiary)
- Coverage dates

**Billing: `billing`**
- CPT/HCPCS codes
- ICD-10 diagnosis codes
- Units, modifiers
- Charges
- Insurance pointers

**Claims: `claims`**
- Claim header information
- Status
- Clearinghouse
- Payer

**Payments: `ar_activity`**
- Payment postings
- Adjustments
- Session ID for batching

#### Document Domain

**Documents: `documents`**
- Document metadata
- Category
- Patient linkage
- Physical file path
- MIME type
- Import date

**Document Categories: `categories`**
- Hierarchical structure
- Access control

#### Security Domain

**Sessions: `session_tracker`**
- Active sessions
- IP address
- Last activity
- Concurrent session management

**Audit Log: `log`**
- User actions
- Patient access
- Record modifications
- IP address
- Timestamp

**API Log: `api_log`**
- API requests
- Request/response data
- Performance metrics

### Data Model Patterns

#### UUID Support
- **Purpose:** Support external data exchange (FHIR, HL7)
- **Format:** Binary(16) UUIDs stored efficiently
- **Generation:** ramsey/uuid library
- **Background Service:** Populates UUIDs for existing records

#### Soft Deletes
- **Pattern:** `activity` flag (1=active, 0=deleted)
- **Benefits:**
  - Data recovery
  - Audit trail preservation
  - Referential integrity maintenance

#### Versioning
- **Forms:** Version tracking for form definitions
- **Documents:** Version history
- **ACL:** Version-based schema updates

#### Multi-tenancy
- **Pattern:** Site-based isolation
- **Implementation:** `/sites/{site_id}/`
- **Isolation:**
  - Separate database per site OR
  - Shared database with site column filtering

---

## API Architecture

### 1. Standard REST API

**Base Path:** `/apis/default/api/`

**Authentication:** OAuth2 Bearer tokens

**Format:** JSON

**Endpoints Pattern:**
```
GET    /api/patient                 # Search patients
GET    /api/patient/:uuid           # Get single patient
POST   /api/patient                 # Create patient
PUT    /api/patient/:uuid           # Update patient
DELETE /api/patient/:uuid           # Delete patient (soft delete)
```

**Resources:**
- patient, practitioner, facility
- appointment, encounter
- allergy, condition, immunization
- prescription, procedure
- insurance, insurance_company
- message, document
- user, list, drug

**Authorization:**
- OAuth2 scopes: `api:oemr`
- ACL enforcement
- User context

**Error Handling:**
- HTTP status codes
- JSON error responses
- Detailed validation errors

### 2. FHIR R4 API

**Base Path:** `/apis/default/fhir/`

**Standard:** FHIR R4 + US Core 3.1

**Authentication:**
- OAuth2 with SMART on FHIR
- Scopes: `patient/*.read`, `user/*.read`, `system/*.read`, etc.

**Capability Statement:**
```
GET /fhir/metadata
```

**Search Operations:**
- Search parameters per resource
- `_include` and `_revinclude`
- `_count` for pagination
- `_sort` for ordering
- Custom search parameters

**FHIR Resources Supported (40+):**

**Patient Compartment:**
- Patient, RelatedPerson, Person
- AllergyIntolerance, Condition, Observation
- MedicationRequest, Medication, Immunization
- Procedure, DiagnosticReport, DocumentReference
- Encounter, Appointment
- Goal, CarePlan, CareTeam
- Coverage

**Provider:**
- Practitioner, PractitionerRole
- Organization, Location

**Administrative:**
- Group, Device
- ServiceRequest, Specimen

**Conformance:**
- Provenance (data provenance tracking)
- ValueSet (code system value sets)

**FHIR Operations:**

**Bulk Data Export ($export):**
```
GET /fhir/$export                    # System export
GET /fhir/Patient/$export            # Patient export
GET /fhir/Group/:id/$export          # Group export
GET /fhir/$bulkdata-status?job=123   # Status check
```

**CCDA Generation ($docref):**
```
GET /fhir/DocumentReference/$docref?patient=123
```

**Search Examples:**
```
GET /fhir/Patient?name=Smith
GET /fhir/Observation?patient=123&category=vital-signs
GET /fhir/MedicationRequest?patient=123&_include=MedicationRequest:medication
```

### 3. SMART on FHIR

**SMART App Launch:** Version 1.1.0

**Discovery Endpoint:**
```
GET /.well-known/smart-configuration
```

**Authorization Endpoints:**
```
GET  /oauth2/default/authorize
POST /oauth2/default/token
POST /oauth2/default/revoke
POST /oauth2/default/introspect
```

**Launch Contexts:**
- EHR Launch (with launch token)
- Standalone Launch (no context)
- Patient context
- Encounter context

**PKCE Support:** For public apps (mobile, single-page apps)

**Grant Types:**
- Authorization Code (recommended)
- Client Credentials (system access)
- Refresh Token

**App Registration:**
```
/interface/smart/register-app.php
```

### 4. HL7 v2.x Interface

**Protocol:** HL7 v2.5 (configurable)

**Message Types:**
- **ORU^R01** - Observation Result (Lab Results)
- **ORM^O01** - Order Message (Lab Orders)
- **ADT^A**** - Admit/Discharge/Transfer (if configured)

**Transmission:**
- File-based
- MLLP (Minimal Lower Layer Protocol)
- Custom interfaces per lab vendor

**Files:**
- `/interface/orders/gen_hl7_order.inc.php` - Generate orders
- `/interface/orders/receive_hl7_results.inc.php` - Receive results

**Library:** aranyasen/hl7 3.2.2

### 5. CCDA API

**Service:** Node.js CCDA generation service

**Endpoint (Internal):**
```
POST /ccdaservice/ccda
```

**Sections Generated:**
- Allergies and Intolerances
- Medications
- Problem List
- Immunizations
- Vital Signs
- Procedures
- Results (Labs)
- Encounters
- Social History
- Care Plan
- Goals

**Standards:**
- CCDA 2.1
- Blue Button+ format
- Schematron validation

**Access Methods:**
1. Direct download from patient chart
2. FHIR DocumentReference.$docref operation
3. Care Coordination module
4. Patient portal download

---

## Security Architecture

### Security Layers

#### 1. Network Security

**Transport Layer:**
- **TLS 1.2+** required for production
- HTTPS enforcement for APIs
- Certificate management

**Firewall:**
- Restrict database access to application servers only
- Limit web server access to necessary ports

#### 2. Authentication

**User Authentication Methods:**

1. **Username/Password**
   - Bcrypt password hashing
   - Configurable password policies
   - Password expiration support
   - Password history

2. **Multi-Factor Authentication (MFA)**
   - TOTP (Time-based One-Time Password)
   - U2F (Universal 2nd Factor)
   - Per-user MFA enforcement

3. **Third-Party Authentication**
   - Google Sign-In (OAuth2)
   - LDAP integration (configurable)

4. **API Authentication**
   - OAuth2 Bearer tokens
   - JWT (JSON Web Tokens)
   - API keys (legacy, discouraged)

**Session Management:**
- Secure session cookies (HttpOnly, Secure, SameSite)
- Session timeout (configurable)
- Concurrent session detection
- Session fixation prevention
- CSRF token validation

#### 3. Authorization

**Access Control List (ACL):**

**System:** phpGACL (Generic Access Control List)

**Components:**
- **ARO** (Access Request Objects) - Users and user groups
- **ACO** (Access Control Objects) - Protected resources
- **AXO** (Access Extension Objects) - Additional context (e.g., patient sensitivity)

**ACL Sections:**
```
admin       - System administration
acct        - Accounting and billing
patients    - Patient information (read/write/addonly)
encounters  - Encounter data
groups      - User groups
calendar    - Scheduling
sensitivities - Data sensitivity levels
lists       - Clinical lists
```

**Granularity:**
- Function-level permissions
- Data-level permissions
- Field-level restrictions (via sensitivity)
- Facility-based restrictions

**Implementation:**
```php
if (!AclMain::aclCheckCore('patients', 'demo')) {
    // Access denied
}
```

#### 4. Data Protection

**Encryption:**

**At Rest:**
- Database encryption (optional, MySQL-level)
- Document encryption (configurable)
- AES-256-CBC for sensitive fields
- OpenSSL cipher required

**In Transit:**
- TLS 1.2+ for all network communication
- Direct messaging encryption
- SFTP for file transfers

**Data Masking:**
- Configurable data masking for non-privileged users
- Social Security Number masking
- Sensitive field hiding

**De-identification:**
- Full de-identification tool (`/interface/de_identification_forms/`)
- HIPAA Safe Harbor method
- Re-identification capability (with proper authorization)

#### 5. Audit and Compliance

**Audit Logging:**

**Events Logged:**
- User login/logout
- Patient record access
- Record modifications (CRUD)
- Document access
- Prescription creation/modification
- Security events
- API access
- Administrative changes

**Log Tables:**
```sql
log                  -- Main event log
api_log              -- API requests
audit_master         -- Detailed audit header
audit_details        -- Detailed audit records
log_comment_encrypt  -- Encrypted log comments
```

**Log Integrity:**
- Tamper detection report
- Checksum validation
- Append-only design

**Compliance Features:**
- HIPAA audit log requirements
- Breach notification tracking
- User activity reports
- Patient access history

#### 6. Input Validation and Output Encoding

**Input Validation:**
- Type validation
- Format validation
- Range validation
- Required field validation
- Custom validators (`/src/Validators/`)

**SQL Injection Prevention:**
- Parameterized queries (PDO, Laminas DB)
- Input sanitization
- ORM usage in modern code

**XSS Prevention:**
- Output encoding via `htmlspecialchars()`
- Custom wrapper: `/library/htmlspecialchars.inc.php`
- Content Security Policy headers (configurable)
- Template auto-escaping (Twig)

**CSRF Protection:**
- Token generation: `CsrfUtils::collectCsrfToken()`
- Token validation: `CsrfUtils::verifyCsrfToken()`
- Per-form unique tokens
- Token expiration

**File Upload Security:**
- MIME type validation
- File extension whitelist
- Virus scanning integration capability
- Upload size limits
- Secure file storage outside web root

#### 7. API Security

**OAuth2 Security:**
- Scoped access tokens
- Short-lived access tokens (1 hour default)
- Refresh token rotation
- Client authentication (client_secret for confidential clients)
- PKCE for public clients
- Token revocation
- Token introspection

**Rate Limiting:**
- Configurable (not enforced by default)
- Per-client rate limiting capability

**API Authorization:**
- Scope-based access control
- Resource-level permissions
- User context enforcement

---

## Integration Architecture

### Integration Patterns

#### 1. RESTful API Integration

**Outbound:**
- Guzzle HTTP client
- Configurable endpoints
- OAuth2 client support
- Retry logic
- Error handling

**Inbound:**
- REST API endpoints
- FHIR API endpoints
- Webhook support (via modules)

#### 2. HL7 v2.x Integration

**Architecture:**
- File-based exchange (default)
- MLLP socket support (via modules)
- Vendor-specific adapters

**Lab Integration:**
- Quest Diagnostics
- LabCorp
- Generic HL7

**Message Flow:**
```
OpenEMR → HL7 ORM → Lab System
Lab System → HL7 ORU → OpenEMR
```

**Parser:** aranyasen/hl7 library

#### 3. FHIR-Based Integration

**Use Cases:**
- Health Information Exchange (HIE)
- Third-party apps (SMART on FHIR)
- Public health reporting
- Research data export

**Bulk Data Export:**
- FHIR $export operation
- NDJSON format
- Asynchronous processing
- Secure file download

#### 4. Direct Messaging

**Purpose:** Secure provider-to-provider messaging

**Protocol:** Direct Trust Protocol

**Components:**
- HISP (Health Information Service Provider) integration
- S/MIME encryption
- Trust anchors
- Certificate management

**Integration:**
- phiMail (background service)
- Message inbox
- Attachment support (CCDA)

#### 5. EDI (Electronic Data Interchange)

**X12 Transactions:**
- **837P** - Professional claims
- **837I** - Institutional claims
- **270** - Eligibility inquiry
- **271** - Eligibility response
- **835** - Electronic remittance advice
- **997** - Functional acknowledgment

**Clearinghouse Integration:**
- Configurable X12 partners
- SFTP transmission
- Batch processing
- File parsing and viewing

**Flow:**
```
OpenEMR → 837 Claim → Clearinghouse → Payer
Payer → 835 ERA → Clearinghouse → OpenEMR
```

#### 6. E-Prescribing Integration

**Modules:**
- Weno eRx (modern, recommended)
- NewCrop (legacy)

**Capabilities:**
- Electronic prescription transmission
- Prescription benefit check
- Medication history retrieval
- Formulary checking
- EPCS (controlled substances)

**Protocol:** SOAP/REST APIs

#### 7. External Services

**SMS/Voice:**
- Twilio
- Clickatell
- RingCentral

**Payment Processing:**
- Stripe
- Authorize.net
- Square

**Telehealth:**
- Custom telehealth modules
- Video conferencing integration

**MedEx:**
- Appointment reminders
- Patient recalls
- Patient engagement

### Integration Security

**All Integrations:**
- Encrypted transmission (TLS)
- Authentication required
- Authorization checking
- Audit logging
- Error handling
- Timeout management

---

## Deployment Architecture

### Deployment Models

#### 1. Self-Hosted (On-Premises)

**Infrastructure:**
- Physical servers or VMs
- Customer-managed infrastructure
- Network isolation

**Benefits:**
- Full data control
- Customization freedom
- No recurring SaaS fees

**Challenges:**
- IT infrastructure required
- Maintenance responsibility
- Backup management
- Security updates

#### 2. Hosted (Cloud)

**Options:**
- AWS, Azure, Google Cloud
- DigitalOcean, Linode
- Healthcare-specific hosting

**Considerations:**
- HIPAA-compliant hosting
- Business Associate Agreement (BAA)
- Backup and disaster recovery
- Scalability

#### 3. Multi-Tenant

**Architecture:**
- Single codebase
- Multiple sites (`/sites/{site_id}/`)
- Shared or separate databases

**Use Cases:**
- MSO (Management Services Organization)
- Multi-location practices
- Hosting providers

**Isolation:**
- Site-based data separation
- Site-specific configuration
- Independent backups

### Deployment Stack

**Typical LAMP Stack:**

```
┌─────────────────────────────────────┐
│        Load Balancer (Optional)      │
│         nginx / HAProxy              │
└─────────────────────────────────────┘
                  │
    ┌─────────────┴─────────────┐
    │                           │
┌─────────────┐         ┌─────────────┐
│  Web Server │         │  Web Server │
│   Apache/   │         │   Apache/   │
│   nginx     │         │   nginx     │
│   PHP-FPM   │         │   PHP-FPM   │
│             │         │             │
│  OpenEMR    │         │  OpenEMR    │
│  codebase   │         │  codebase   │
└─────────────┘         └─────────────┘
       │                       │
       └───────────┬───────────┘
                   │
         ┌─────────────────┐
         │  MySQL/MariaDB  │
         │    Database     │
         │    (Primary)    │
         └─────────────────┘
                   │
         ┌─────────────────┐
         │  MySQL/MariaDB  │
         │    Database     │
         │   (Replica)     │
         └─────────────────┘
```

**Additional Components:**

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Node.js   │     │    Redis    │     │   Backup    │
│    CCDA     │     │    Cache    │     │   Storage   │
│   Service   │     │   (Optional)│     │             │
└─────────────┘     └─────────────┘     └─────────────┘
```

### System Requirements

**Web Server:**
- Apache 2.4+ with mod_rewrite OR
- Nginx 1.14+

**PHP:**
- Version: 8.2+
- Extensions:
  - curl, gd, json, mbstring, mysqli, openssl
  - xml, zip, zlib, fileinfo, PDO
  - sodium, intl, ldap (optional)
- Configuration:
  - memory_limit: 512M+ recommended
  - max_execution_time: 300+
  - upload_max_filesize: 32M+
  - post_max_size: 32M+

**Database:**
- MySQL 5.7.8+ OR MariaDB 10.2.2+
- InnoDB storage engine
- 2GB+ RAM recommended
- Regular backups

**Node.js:**
- Version: 14+
- For CCDA service
- pm2 for process management (recommended)

**Disk Space:**
- Application: 500MB
- Documents: Based on volume (plan 10GB+ minimum)
- Database: Based on patients (plan 5GB+ minimum)
- Backups: 3x database + documents size

**Memory:**
- Small practice (<1000 patients): 4GB RAM
- Medium practice (1000-10000): 8GB RAM
- Large practice (10000+): 16GB+ RAM

### Scalability Considerations

**Horizontal Scaling:**
- Multiple web servers behind load balancer
- Shared file storage (NFS, S3, etc.)
- Database replication (read replicas)
- Session storage in database or Redis

**Vertical Scaling:**
- Increase server resources (CPU, RAM)
- Optimize MySQL configuration
- PHP opcode caching (OPcache)

**Performance Optimization:**
- Redis caching
- CDN for static assets
- Database indexing
- Query optimization
- Nginx reverse proxy caching

---

## Extensibility Architecture

### Extension Mechanisms

#### 1. Module System

**Types:**

**Zend Modules:**
- Full Laminas MVC architecture
- Complete control over routing
- Database tables
- ACL integration
- Example: Care Coordination, Immunization Registry

**Custom Modules:**
- Simpler structure
- Menu integration
- Page rendering
- Examples: Weno eRx, Telehealth

**Installation:**
```
composer require <vendor>/<module-name>
```

**Module Structure:**
```
/interface/modules/custom_modules/<module-name>/
├── moduleConfig.php      # Module metadata
├── src/                  # PHP classes
├── templates/            # UI templates
├── public/              # Assets
└── sql/                 # Database schema
```

#### 2. Event System

**Framework:** Symfony EventDispatcher

**Event Types:**
- Patient events
- Encounter events
- Appointment events
- Prescription events
- User events
- Document events

**Usage:**

**Dispatching:**
```php
use OpenEMR\Events\Patient\PatientCreatedEvent;

$event = new PatientCreatedEvent($patient);
$dispatcher->dispatch($event, PatientCreatedEvent::EVENT_NAME);
```

**Subscribing:**
```php
class MyEventSubscriber implements EventSubscriberInterface
{
    public static function getSubscribedEvents()
    {
        return [
            PatientCreatedEvent::EVENT_NAME => 'onPatientCreated',
        ];
    }

    public function onPatientCreated(PatientCreatedEvent $event)
    {
        // Custom logic
    }
}
```

#### 3. Form Builder

**Layout-Based Forms (LBF):**

**Features:**
- Visual form designer
- Custom field types
- Conditional logic
- Data source binding
- Multi-page forms

**Field Types:**
- Text, textarea, number
- Date, datetime
- Select, multiselect
- Radio, checkbox
- Provider, facility
- Custom data types

**Use Cases:**
- Custom intake forms
- Specialty-specific documentation
- Assessment tools

#### 4. API Extensions

**Custom Endpoints:**
- Add to `/_rest_routes.inc.php`
- Implement controller
- Apply authorization
- Return standardized response

**FHIR Extensions:**
- Custom search parameters
- Extension elements
- Custom operations

#### 5. Custom Reports

**Report Framework:**
- SQL-based reports
- Parameter input
- Output formats (HTML, PDF, CSV)
- Scheduled execution capability

**Location:** `/interface/reports/`

#### 6. Hooks

**Types:**
- UI hooks (inject content into pages)
- Process hooks (modify workflow)
- Data hooks (transform data)

**Implementation:** Via event system

#### 7. Custom Rules (CDR)

**Clinical Decision Rules:**

**Components:**
- Rule definitions
- Target population
- Numerator/denominator logic
- Interventions
- Reminders

**Location:** `/library/classes/rulesets/`

**Use Cases:**
- Custom quality measures
- Practice-specific alerts
- Population health management

---

## Performance and Scalability

### Performance Characteristics

**Response Times (Typical):**
- Page load: 1-3 seconds
- API requests: 100-500ms
- FHIR searches: 200-1000ms
- Report generation: 5-30 seconds

### Optimization Strategies

#### 1. Database Optimization

**Indexing:**
- Primary keys on all tables
- Foreign key indexes
- Composite indexes for common queries
- Full-text indexes for search

**Query Optimization:**
- Avoid N+1 queries
- Use joins instead of multiple queries
- Limit result sets
- Pagination for large datasets

**Connection Pooling:**
- Persistent connections
- Connection reuse
- Pool size tuning

#### 2. Application Optimization

**PHP Optimization:**
- OPcache enabled
- Realpath cache tuning
- Class autoloading optimization
- Session handling optimization

**Caching:**
- Redis for session storage
- Redis for object caching
- Query result caching
- Configuration caching

#### 3. Frontend Optimization

**Asset Optimization:**
- Minification (CSS, JS)
- Compression (gzip)
- CDN for static assets
- Lazy loading images

**HTTP Optimization:**
- HTTP/2
- Keep-alive connections
- Caching headers
- ETags

#### 4. Scalability Patterns

**Read Scaling:**
- Database read replicas
- Cache layer (Redis)
- Load balancing

**Write Scaling:**
- Database optimization
- Batch processing
- Async processing (background jobs)

**File Storage:**
- Shared storage (NFS, S3)
- Distributed file systems
- CDN for document delivery

### Monitoring

**Application Monitoring:**
- Error logging (Monolog)
- Performance metrics
- API response times
- Background service status

**Database Monitoring:**
- Slow query log
- Connection count
- Table locks
- Replication lag

**Server Monitoring:**
- CPU, memory usage
- Disk I/O
- Network traffic
- Process count

---

## Compliance and Standards

### Healthcare Standards

#### FHIR (Fast Healthcare Interoperability Resources)
- **Version:** R4
- **Profile:** US Core 3.1
- **Compliance:** ONC Certification
- **Capabilities:**
  - RESTful API
  - Search
  - Bulk data export
  - Provenance
  - SMART on FHIR

#### HL7 v2.x
- **Versions Supported:** 2.3, 2.5, 2.5.1
- **Message Types:** ORU, ORM, ADT
- **Use Cases:** Lab interfaces, ADT feeds

#### CCDA (Consolidated Clinical Document Architecture)
- **Version:** 2.1
- **Sections:**
  - Allergies, Medications, Problems
  - Immunizations, Vitals, Results
  - Encounters, Procedures, Social History
  - Care Plan, Goals
- **Use Cases:**
  - Transitions of care
  - Patient data sharing
  - HIE participation

#### X12 EDI
- **Transactions:**
  - 270/271 - Eligibility
  - 837P/837I - Claims
  - 835 - Remittance
  - 997 - Acknowledgment
- **Compliance:** HIPAA 5010

### Regulatory Compliance

#### HIPAA (Health Insurance Portability and Accountability Act)

**Privacy Rule:**
- Access controls
- Minimum necessary principle
- Patient consent tracking
- Accounting of disclosures

**Security Rule:**
- Administrative safeguards (ACL, audit logs)
- Physical safeguards (facility-dependent)
- Technical safeguards (encryption, authentication)

**Breach Notification:**
- Breach log tracking
- Patient notification capability
- HHS reporting capability

#### ONC Certification

**Edition:** 2015 Edition

**Certified Criteria:**
- Clinical Quality Measures (CQM)
- Patient Access API (FHIR)
- Data Portability (bulk export)
- Standardized API (SMART on FHIR)
- Audit logs
- Clinical decision support

#### 21st Century Cures Act

**Information Blocking Prevention:**
- API access for patients
- API access for third parties
- No fees for API access
- Data portability

**Patient Access:**
- FHIR API
- Bulk data export
- EHI Exporter module
- Patient portal access

### Security Standards

**Authentication:**
- OAuth 2.0 (RFC 6749)
- OpenID Connect
- SMART on FHIR App Launch

**Encryption:**
- TLS 1.2+ for transmission
- AES-256 for data at rest
- S/MIME for Direct messaging

**Password Management:**
- Bcrypt hashing
- Password complexity requirements
- MFA support

---

## Conclusion

OpenEMR represents a mature, comprehensive EHR system with a well-architected foundation that balances legacy compatibility with modern development practices. The system's layered architecture, robust API support, comprehensive security model, and standards compliance make it suitable for a wide range of healthcare practice settings.

### Key Strengths

1. **Standards Compliance:** Full support for FHIR R4, HL7, CCDA, X12 EDI
2. **Security:** Multi-layered security with ACL, audit logging, encryption
3. **Extensibility:** Modules, events, custom forms, APIs
4. **Internationalization:** Support for 70+ languages
5. **Open Source:** Free, customizable, community-driven
6. **ONC Certified:** Meets federal requirements

### Architectural Evolution

The codebase shows evolution from legacy procedural PHP to modern object-oriented, service-based architecture:
- **Legacy:** `/library/`, `/interface/` (procedural, global functions)
- **Modern:** `/src/` (PSR-4, services, events, FHIR)

This hybrid approach ensures:
- Backward compatibility
- Smooth migration path
- Preservation of stability
- Adoption of best practices

### Future Considerations

**Recommended Architectural Improvements:**
1. Further migration to service layer
2. Enhanced caching strategies
3. Microservices for specific functions (CCDA, FHIR)
4. GraphQL API consideration
5. Enhanced frontend frameworks (Vue.js, React)
6. Container-based deployment (Docker, Kubernetes)

---

**Document Version:** 1.0
**Date:** 2025-11-13
**Prepared For:** OpenEMR Technical Stakeholders
