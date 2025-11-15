# Patient Management

**Capability ID:** 01
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Patient Management provides comprehensive functionality for managing patient demographic information, medical record numbers, insurance details, contacts, and administrative data. This capability serves as the foundation for all clinical and administrative activities within OpenEMR.

**Primary Purpose:** Enable healthcare staff to register, search, update, and maintain complete patient demographic and administrative records.

**Primary Users:** Front desk staff, registration clerks, medical assistants, nurses, providers, billing staff

---

## Use Cases

1. **New Patient Registration** - Register new patients with complete demographic and insurance information
2. **Patient Search and Selection** - Quickly locate existing patients by name, DOB, MRN, or other identifiers
3. **Patient Information Update** - Maintain current contact information, insurance, and demographics
4. **Insurance Management** - Manage primary, secondary, and tertiary insurance coverage
5. **Portal Account Management** - Set up and manage patient portal access credentials
6. **Patient Merge** - Consolidate duplicate patient records
7. **Emergency Contact Management** - Maintain emergency contact and next-of-kin information

---

## Features

### Feature 1.1: Patient Registration

**Description:**
Complete patient registration with demographic information, contact details, insurance, emergency contacts, pharmacy preferences, and medical history.

**How to Use (User Instructions):**
1. Navigate to Patient/Client → New/Search
2. Click "New Patient" or press the "New" button
3. Fill in required demographic fields (First Name, Last Name, Date of Birth, Sex)
4. Enter contact information (address, phone numbers, email)
5. Select primary care provider and pharmacy
6. Add insurance information (Policy, Subscriber, Group Number)
7. Enter emergency contact details
8. Add employer information if applicable
9. Set patient portal credentials if creating portal access
10. Click "Save" to create patient record
11. System assigns unique Patient ID (PID) and generates UUID

**How to Test:**
- **Test 1:** Create patient with minimum required fields (name, DOB, sex)
- **Test 2:** Create patient with complete information including multiple insurance policies
- **Test 3:** Verify duplicate patient warning triggers for similar names/DOB
- **Test 4:** Confirm UUID is automatically generated
- **Test 5:** Test required field validation (attempt to save without required fields)
- **Test 6:** Verify audit log entry is created for new patient registration

**Expected System Behavior:**
- System validates required fields before allowing save
- Duplicate patient detection algorithm runs on name + DOB match
- If potential duplicate detected, warning displayed to user
- Unique Patient ID (PID) auto-incremented from database sequence
- UUID generated using ramsey/uuid library
- Audit log entry created in `log` table with event "new-patient-record"
- If portal credentials provided, record created in `patient_access_onsite` table
- Patient demographics stored in `patient_data` table
- Insurance data stored in `insurance_data` table
- Emergency contacts stored in appropriate patient_data fields

**Technical Details:**
- **Primary File:** `/interface/new/new.php`
- **Controller:** `/controllers/C_Patient.class.php`
- **Service:** `/src/Services/PatientService.php`
- **Database Table:** `patient_data`
- **Related Tables:** `insurance_data`, `insurance_companies`, `patient_access_onsite`

---

### Feature 1.2: Patient Search

**Description:**
Multi-criteria search functionality to quickly locate patient records using name, date of birth, medical record number, phone number, SSN, or other identifiers.

**How to Use (User Instructions):**
1. Navigate to Patient/Client → New/Search
2. Enter search criteria in any combination:
   - Last Name, First Name
   - Date of Birth
   - Medical Record Number (PID or External ID)
   - Phone Number
   - Social Security Number
   - Patient ID
3. Click "Search" or press Enter
4. Review search results in table format
5. Click on patient name to select and open patient chart
6. Use "AND" vs "OR" toggle to refine multi-field searches

**How to Test:**
- **Test 1:** Search by last name only (should return all matching patients)
- **Test 2:** Search by exact full name
- **Test 3:** Search by date of birth
- **Test 4:** Search by medical record number (PID)
- **Test 5:** Search using partial name (wildcard search)
- **Test 6:** Test AND logic (name + DOB must both match)
- **Test 7:** Test OR logic (name OR DOB matches)
- **Test 8:** Search with no results, verify appropriate message
- **Test 9:** Test search performance with 10,000+ patient records

**Expected System Behavior:**
- Search executes SQL query against `patient_data` table
- Wildcards automatically added for name searches (partial match)
- Exact match required for PID, SSN, DOB
- Phone number search strips non-numeric characters for comparison
- Results sorted by last name, first name by default
- Maximum 100 results returned by default (configurable)
- If single exact match, patient chart may auto-open (configurable)
- Search respects facility restrictions if user has facility-based ACL
- Inactive/deceased patients may be excluded or flagged (configurable)
- Search query logged for audit purposes

**Technical Details:**
- **Primary File:** `/interface/new/new.php`, `/library/patient.inc.php`
- **Service:** `/src/Services/PatientService.php` - `search()` method
- **Database Table:** `patient_data`
- **Search Algorithm:** SQL LIKE with wildcards for text, exact match for IDs
- **Performance:** Indexed on pid, fname, lname, DOB, pubpid, ss

---

### Feature 1.3: Patient Demographics Management

**Description:**
Comprehensive demographic data management including personal information, contact details, race/ethnicity, language preferences, marital status, occupation, and other administrative data.

**How to Use (User Instructions):**
1. Open patient chart (search and select patient)
2. Navigate to Demographics section
3. Click "Edit" button to enable editing
4. Update any demographic fields:
   - Name, title, suffix
   - Date of birth, sex, gender identity
   - Address (street, city, state, ZIP)
   - Phone numbers (home, cell, work)
   - Email address
   - Race, ethnicity
   - Preferred language
   - Marital status, occupation
   - Social Security Number
5. Click "Save" to commit changes
6. System displays updated information and confirmation

**How to Test:**
- **Test 1:** Update patient address and verify change saved
- **Test 2:** Update phone numbers and email, verify portal email notification sent if configured
- **Test 3:** Change preferred language, verify chart language updates
- **Test 4:** Update race/ethnicity, verify CCDA export includes updated values
- **Test 5:** Test validation rules (email format, ZIP code format, phone format)
- **Test 6:** Verify audit log captures all field changes
- **Test 7:** Test with ACL user having read-only access (should not allow edits)

**Expected System Behavior:**
- Edit mode enabled only for authorized users (ACL check: `patients`, `demo` write permission)
- Field validation applied before save (email regex, phone format, date validation)
- Changes written to `patient_data` table
- Audit log entry created in `log` table with before/after values
- If email changed, patient portal login email updated
- Race/ethnicity values conform to CDC codes
- Language values from ISO 639 language codes
- UUID preserved during updates
- Modification timestamp (`date` field) updated
- If demographics affect FHIR resources, FHIR provenance created

**Technical Details:**
- **Primary File:** `/interface/patient_file/summary/demographics.php`
- **Form File:** `/interface/patient_file/summary/demographics_full.php`
- **Service:** `/src/Services/PatientService.php` - `update()` method
- **Database Table:** `patient_data`
- **Validation:** `/src/Validators/PatientValidator.php`
- **Audit:** Event type "update-patient-record"

---

### Feature 1.4: Insurance Management

**Description:**
Management of patient insurance coverage including primary, secondary, and tertiary insurance policies with subscriber information, policy details, and coverage dates.

**How to Use (User Instructions):**
1. Open patient chart
2. Navigate to Demographics → Insurance section or Insurance tab
3. Click "Edit" or "Add Insurance"
4. Select insurance priority (Primary, Secondary, or Tertiary)
5. Search and select insurance company from directory
6. Enter policy information:
   - Policy Number
   - Group Number
   - Subscriber Name, DOB, Address (if different from patient)
   - Subscriber relationship to patient
   - Effective dates (from/to)
   - Copay amounts
7. Click "Save"
8. Verify insurance card image upload if available
9. Insurance appears in patient demographics and encounter billing

**How to Test:**
- **Test 1:** Add primary insurance, verify saved correctly
- **Test 2:** Add secondary and tertiary insurance, verify priority order maintained
- **Test 3:** Update insurance policy number, verify change reflected in billing
- **Test 4:** Test subscriber different from patient, verify subscriber demographics stored
- **Test 5:** Upload insurance card image, verify image accessible
- **Test 6:** Test insurance effective dates, verify claims use correct insurance for service date
- **Test 7:** Perform eligibility check (270/271), verify response processed
- **Test 8:** Inactivate insurance policy, verify not used for new encounters

**Expected System Behavior:**
- Insurance data stored in `insurance_data` table with type field (primary/secondary/tertiary)
- Only one active insurance per type allowed
- Insurance company must exist in `insurance_companies` table or be created
- Subscriber information stored if different from patient
- Policy dates validated (to_date >= from_date)
- Insurance card images stored in document system with special category
- Eligibility check triggers X12 270 transaction if configured
- Claims (837) reference insurance via pointer
- Copay amounts used in fee sheet/billing
- Audit log entry created for insurance add/update

**Technical Details:**
- **Primary File:** `/interface/patient_file/summary/insurance_edit.php`
- **Service:** `/src/Services/InsuranceService.php`
- **Database Tables:** `insurance_data`, `insurance_companies`
- **Related:** `/interface/billing/edi_270.php` for eligibility checks
- **Image Storage:** Documents system, category "Insurance"

---

### Feature 1.5: Patient Portal Account Management

**Description:**
Creation and management of patient portal access credentials allowing patients secure online access to their medical records, lab results, messaging, and appointments.

**How to Use (User Instructions):**
1. Open patient chart
2. Navigate to Administration → Portal Login
3. Create portal credentials:
   - Enter username (or auto-generate)
   - Enter temporary password
   - Set password expiration to force change on first login
4. Click "Save"
5. Provide credentials to patient via secure method
6. Patient receives portal invitation email if configured
7. Monitor portal access in Portal Activity logs
8. Reset password if patient locked out or forgot credentials
9. Disable portal access if needed

**How to Test:**
- **Test 1:** Create portal account, verify credentials created
- **Test 2:** Login to patient portal using credentials, verify access granted
- **Test 3:** Test password reset functionality
- **Test 4:** Disable portal access, verify patient cannot login
- **Test 5:** Enable portal access after disable, verify re-enabled
- **Test 6:** Test auto-generated username creation
- **Test 7:** Verify portal invitation email sent if configured
- **Test 8:** Test portal MFA setup if enabled

**Expected System Behavior:**
- Portal credentials stored in `patient_access_onsite` table
- Password hashed using bcrypt (via PHP password_hash)
- Username must be unique across all portal users
- Email from `patient_data` used for portal communications
- Portal access controlled by ACL and global settings
- Audit log entry created when portal access granted/revoked
- Portal login attempts logged in separate portal audit log
- Password expiration enforced if configured
- MFA can be required for portal access
- Patient portal login URL: `/portal/`

**Technical Details:**
- **Primary File:** `/interface/patient_file/summary/create_portallogin.php`
- **Portal Login:** `/portal/index.php`
- **Database Table:** `patient_access_onsite`
- **Related Tables:** `patient_data` (email), `patient_access_offsite` (if using offsite portal)
- **Security:** Bcrypt hashing, session management, optional MFA

---

### Feature 1.6: Patient Search by Demographics

**Description:**
Advanced search capability allowing complex queries across multiple demographic fields with AND/OR logic for population health management and quality measure identification.

**How to Use (User Instructions):**
1. Navigate to Reports → Patient List Creation
2. Select search criteria from available fields:
   - Age range
   - Sex/Gender
   - Race/Ethnicity
   - Language
   - Insurance type
   - Primary provider
   - Facility
   - Active/Inactive status
3. Choose AND or OR logic for multiple criteria
4. Click "Submit" to generate patient list
5. Export results to CSV or print
6. Save search criteria as named query for reuse

**How to Test:**
- **Test 1:** Search for all patients age 65+ (Medicare eligibility)
- **Test 2:** Search for Spanish-speaking patients
- **Test 3:** Search female patients age 40-50 for mammography outreach
- **Test 4:** Search patients with specific insurance
- **Test 5:** Search patients by provider
- **Test 6:** Combine multiple criteria with AND logic
- **Test 7:** Export results and verify data accuracy
- **Test 8:** Test saved query functionality

**Expected System Behavior:**
- Query builder constructs SQL WHERE clause from criteria
- Age calculated from DOB relative to current date or specified date
- Race/ethnicity values matched against standardized codes
- Results respect facility-based ACL restrictions
- Results can be exported to CSV format
- Maximum result set size enforced (default 10,000)
- Query execution logged for audit
- Saved queries stored in database for reuse
- Results can feed into batch communication system

**Technical Details:**
- **Primary File:** `/interface/reports/patient_list_creation.php`
- **Query Builder:** Dynamic SQL construction with parameterized queries
- **Database Table:** `patient_data` (primary)
- **Export:** PHPSpreadsheet for CSV generation
- **Related:** Integration with batch communication for patient outreach

---

### Feature 1.7: Patient Merge (Duplicate Resolution)

**Description:**
Ability to identify and merge duplicate patient records, consolidating all clinical data, encounters, documents, and billing information into a single unified patient record.

**How to Use (User Instructions):**
1. Navigate to Administration → Patient Merge (if available)
2. Search for suspected duplicate patients
3. Select source patient (record to be merged)
4. Select target patient (record to keep)
5. Review data comparison side-by-side
6. Confirm merge operation
7. System consolidates all data to target patient
8. Source patient marked as merged/inactive
9. All references updated to target patient
10. Audit log records merge transaction

**How to Test:**
- **Test 1:** Create two duplicate test patients with different encounters
- **Test 2:** Merge duplicates, verify all encounters appear under target patient
- **Test 3:** Verify prescriptions transferred correctly
- **Test 4:** Verify documents transferred correctly
- **Test 5:** Verify billing records transferred correctly
- **Test 6:** Verify insurance information consolidated
- **Test 7:** Check audit log for complete merge record
- **Test 8:** Verify source patient marked inactive/merged
- **Test 9:** Verify FHIR API returns target patient for merged UUID

**Expected System Behavior:**
- **WARNING:** Patient merge is a critical operation requiring elevated permissions
- System validates no merge is performed if active billing on both records (configurable)
- All foreign key references updated in database:
  - Encounters (form_encounter.pid)
  - Prescriptions (prescriptions.patient_id)
  - Documents (documents.foreign_id)
  - Appointments (openemr_postcalendar_events.pc_pid)
  - Billing (billing.pid)
  - All clinical lists (lists.pid)
- Source patient record flagged in patient_data (possibly with merged_to_pid field)
- Comprehensive audit log entry created with both PIDs
- UUID mapping maintained for FHIR consistency
- Transaction wrapped in database transaction (rollback on error)
- User must have admin-level ACL permission

**Technical Details:**
- **Primary File:** May be in custom module or admin interface
- **Database Impact:** Updates across 50+ tables
- **Transaction Safety:** MySQL InnoDB transaction
- **Audit:** Comprehensive logging of all moved records
- **Rollback:** Must be possible to undo merge
- **Note:** Patient merge functionality varies by installation; may require module

---

## Configuration

### Global Settings

**Location:** Administration → Globals → Features

- **Enable Portal** - Allow patient portal functionality
- **Duplicate Patient Check** - Sensitivity for duplicate detection (strict/moderate/loose)
- **Auto-assign Patient ID** - Use auto-increment PID vs manual entry
- **Patient ID Display** - Show internal PID, external MRN, or both
- **UUID Auto-generation** - Background service to populate UUIDs
- **Patient Photo** - Enable patient photo upload
- **Deceased Patient Handling** - Mark deceased patients, exclude from searches
- **Patient Portal URL** - Custom URL for patient portal access

### ACL Requirements

- **Patient Demo (Read)** - `patients/demo` - View patient demographics
- **Patient Demo (Write)** - `patients/demo/write` - Edit patient demographics
- **Patient Demo (Add Only)** - `patients/demo/addonly` - Create new patients only
- **Admin** - `admin/super` - Required for patient merge

---

## Related Capabilities

- **[Appointment Scheduling](03-appointment-scheduling.md)** - Patient selection for appointments
- **[Billing and Revenue Cycle](04-billing-revenue-cycle.md)** - Insurance and guarantor information
- **[Patient Portal](09-patient-portal.md)** - Patient self-service access
- **[Clinical Documentation](02-clinical-documentation.md)** - Clinical data linked to patients
- **[Messaging and Communication](11-messaging-communication.md)** - Patient contact information

---

## Compliance and Standards

- **HIPAA** - Patient demographic data protected under Privacy Rule
- **FHIR R4** - Patient resource conforms to US Core Patient profile
- **ONC Certification** - Patient demographics required for certified EHR
- **CDC Codes** - Race and ethnicity codes conform to CDC standards
- **HL7** - Patient demographics exported in HL7 ADT messages
- **CCDA** - Patient demographics included in all CCDA documents

---

## Technical Implementation

### Primary Database Schema

**Table: patient_data**
```sql
CREATE TABLE `patient_data` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `uuid` binary(16) DEFAULT NULL,
  `title` varchar(255) DEFAULT NULL,
  `language` varchar(255) DEFAULT NULL,
  `financial` varchar(255) DEFAULT NULL,
  `fname` varchar(255) DEFAULT NULL,
  `lname` varchar(255) DEFAULT NULL,
  `mname` varchar(255) DEFAULT NULL,
  `DOB` date DEFAULT NULL,
  `street` varchar(255) DEFAULT NULL,
  `postal_code` varchar(255) DEFAULT NULL,
  `city` varchar(255) DEFAULT NULL,
  `state` varchar(255) DEFAULT NULL,
  `country_code` varchar(255) DEFAULT NULL,
  `drivers_license` varchar(255) DEFAULT NULL,
  `ss` varchar(255) DEFAULT NULL,
  `occupation` longtext DEFAULT NULL,
  `phone_home` varchar(255) DEFAULT NULL,
  `phone_biz` varchar(255) DEFAULT NULL,
  `phone_contact` varchar(255) DEFAULT NULL,
  `phone_cell` varchar(255) DEFAULT NULL,
  `pharmacy_id` int(11) DEFAULT NULL,
  `status` varchar(255) DEFAULT NULL,
  `contact_relationship` varchar(255) DEFAULT NULL,
  `date` datetime DEFAULT NULL,
  `sex` varchar(255) DEFAULT NULL,
  `gender_identity` varchar(255) DEFAULT NULL,
  `sexual_orientation` varchar(255) DEFAULT NULL,
  `race` varchar(255) DEFAULT NULL,
  `ethnicity` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `email_direct` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uuid` (`uuid`),
  KEY `pid` (`id`)
);
```

### Key Service Methods

**PatientService.php:**
- `getAll($search, $isAndCondition)` - Search patients
- `getOne($uuid)` - Get single patient by UUID
- `insert($data)` - Create new patient
- `update($uuid, $data)` - Update patient demographics
- `getPatientPictureUrl($pid)` - Get patient photo

---

**End of Patient Management Capability**
