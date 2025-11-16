# Practice Administration

**Capability ID:** 10
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Practice Administration provides system administration tools for managing users, facilities, access control, code systems, configurations, and system maintenance supporting multi-user, multi-facility healthcare organizations.

**Primary Purpose:** Enable system administrators to configure and maintain OpenEMR, manage users and security, configure facilities and providers, maintain code systems, and monitor system health.

**Primary Users:** System administrators, practice managers, IT staff

---

## Key Features

### User Management
- Create, edit, delete user accounts
- Assign usernames and passwords (bcrypt hashed)
- Configure user roles and permissions
- Set provider NPI and specialty
- Assign users to facilities
- Multi-factor authentication (MFA) setup (TOTP, U2F)
- User groups for bulk permission assignment
- Inactive user management

**Files:** `/interface/usergroup/user_admin.php`, `/interface/usergroup/user_info.php`
**Tables:** `users`, `gacl_aro` (access request objects)

### Access Control List (ACL)
- Role-based access control
- Granular permissions (admin, patients, encounters, billing, etc.)
- Group-based permission assignment
- ACL sections: admin, acct, patients, encounters, sensitivities, etc.
- Permission inheritance
- Object-level security (ARO, ACO, AXO model)

**Files:** `/interface/usergroup/adminacl.php`, `/src/Common/Acl/`
**System:** phpGACL (Generic Access Control List)

### Facility Management
- Create and configure medical facilities
- Facility demographics (name, address, tax ID, NPI)
- Billing settings per facility
- POS (place of service) codes
- Facility hours
- Assign users to facilities
- Multi-facility support

**Files:** `/interface/usergroup/facilities.php`, `/interface/usergroup/facility_admin.php`
**Tables:** `facility`, `facility_user_ids`

### Global Settings Configuration
- 180+ configurable settings across categories:
  - Appearance (themes, logos)
  - Locale (timezone, date formats)
  - Features (portal, billing, scheduling)
  - Security (session timeout, password rules)
  - Notifications (email, SMS)
  - Billing (fee schedules, claim formats)
  - Calendar (appointment settings)
  - API/FHIR/OAuth2 settings

**Files:** `/interface/super/edit_globals.php`, `/library/globals.inc.php`
**Table:** `globals`

### Code System Management
- Import and manage medical code sets:
  - **ICD-10-CM** - Diagnosis codes
  - **SNOMED CT** - Clinical terminology
  - **RxNorm** - Medication codes
  - **CPT** - Procedure codes
  - **LOINC** - Lab observation codes
  - **CVX** - Vaccine codes
  - **CQM Value Sets** - Quality measure code sets
- Code search and maintenance
- Custom code lists
- Fee schedule assignment to CPT codes

**Files:** `/interface/code_systems/`, `/interface/super/edit_list.php`
**Tables:** `icd10_dx_order_code`, `sct_concepts`, `codes`, etc.

### Language and Translation Management
- Manage 70+ supported languages
- Translation string editing
- Language constant definitions
- Add new languages
- Set default language

**Files:** `/interface/language/`
**Tables:** `lang_definitions`, `lang_constants`

### Backup and Maintenance
- Database backup (manual or scheduled)
- Backup log viewing
- Database size monitoring
- System health checks
- Log file management

**Files:** `/interface/main/backup.php`, `/interface/main/backuplog.php`

### Log Viewing
- System event log viewer
- API request log
- eRx transaction log
- Audit log search and filter
- Tamper detection

**Files:** `/interface/logview/`
**Tables:** `log`, `api_log`

### SSL Certificate Management
- Upload and manage SSL certificates for Direct messaging and API security
**Files:** `/interface/usergroup/ssl_certificates_admin.php`

### Address Book
- Provider directory
- External provider contacts
- Referral source management
- NPI lookup integration

**Files:** `/interface/usergroup/addrbook_list.php`

---

## Configuration

**Global Settings:**
All configuration in Administration → Globals across multiple tabs

**ACL Requirements:**
- User Management - `admin/users`
- Facility Management - `admin/practice`
- Global Settings - `admin/super`
- ACL Management - `admin/acl`
- Code Systems - `admin/super`
- Backups - `admin/super`

---

## Related Capabilities

- [Security and Compliance](16-security-compliance.md) - Security settings
- [Patient Management](01-patient-management.md) - Facility assignment
- [Billing](04-billing-revenue-cycle.md) - Fee schedules, code systems
- [Internationalization](18-internationalization.md) - Language management

---

## Compliance

- **HIPAA** - User access controls, audit logging
- **ONC** - Administrative capabilities for certified EHR

---

**End of Practice Administration Capability**
