# Pharmacy and Prescription Management

**Capability ID:** 06
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Pharmacy and Prescription Management provides electronic prescribing (eRx), prescription tracking, drug inventory management, and pharmacy integration supporting safe medication ordering and dispensing.

**Primary Purpose:** Enable providers to prescribe medications electronically, manage drug inventory for dispensing practices, track controlled substances, and integrate with pharmacy systems and e-prescribing networks.

**Primary Users:** Providers, nurses, pharmacists, medical assistants

---

## Use Cases

1. **Electronic Prescribing** - Send prescriptions electronically to pharmacies
2. **Prescription Management** - Create, track, and renew prescriptions
3. **Drug Inventory Management** - Track medication stock for dispensing
4. **Controlled Substance Prescribing** - EPCS (Electronic Prescribing for Controlled Substances)
5. **Medication History** - Retrieve patient medication history from pharmacies
6. **Formulary Checking** - Check insurance formularies and alternatives
7. **Drug-Drug Interaction Checking** - Clinical decision support for prescribing
8. **Prescription Printing** - Print paper prescriptions when needed

---

## Key Features

### Electronic Prescribing (eRx)
- Integration with e-prescribing networks (Weno, NewCrop)
- Send prescriptions directly to patient's pharmacy
- Real-time prescription benefit check
- Formulary and prior authorization information
- Medication history retrieval (Rx fill history)
- Electronic prior authorization (ePA)
- Controlled substance prescribing (EPCS) with enhanced identity proofing
- Prescription status tracking (transmitted, received, filled)

**Module:** `/interface/modules/custom_modules/oe-module-weno/`
**Requirements:** Weno account or NewCrop integration

### Prescription Creation and Management
- Search medications by name or RxNorm code
- Select strength, dosage form, route
- Enter SIG (directions for use)
- Set quantity and refills
- Document indication
- Print or electronically transmit
- Track prescription status
- Renew and modify prescriptions
- Cancel/discontinue prescriptions

**Files:** `/controllers/C_Prescription.class.php`
**Tables:** `prescriptions`
**Service:** `/src/Services/PrescriptionService.php`

### Drug Inventory Management
- Maintain drug catalog with NDC codes
- Track lot numbers and expiration dates
- Record medication dispensing
- Inventory adjustments (additions, returns, waste)
- Low stock alerts
- Controlled substance logging (DEA compliance)
- Destruc tion documentation
- Drug sales and billing

**Files:** `/interface/drugs/`
**Tables:** `drug_inventory`, `drug_sales`, `drug_templates`
**Reports:** Inventory activity, destroyed drugs, sales by item

### Formulary and Prior Authorization
- Check patient's insurance formulary
- Identify preferred alternatives
- View tier levels and copay amounts
- Submit electronic prior authorization requests
- Track PA status
- Alternative medication suggestions

**Integration:** Via e-prescribing module

### Drug Interaction Checking
- Check for drug-drug interactions
- Check for drug-allergy interactions
- Display warnings during prescribing
- Severity levels (contraindicated, serious, moderate, minor)
- Override with documentation

**Integration:** Via e-prescribing module or custom drug database

---

## Configuration

**Global Settings:**
- E-Prescribing Provider - Weno, NewCrop, or other
- Pharmacy Directory - Default and patient-preferred pharmacies
- Controlled Substance Settings - EPCS requirements, DEA numbers
- Prescription Format - Layout for printed prescriptions
- Drug Database - RxNorm integration
- Interaction Checking - Enable/disable, severity thresholds

**ACL Requirements:**
- Prescribe - `patients/med/write`
- View Prescriptions - `patients/med`
- Manage Inventory - `admin/drugs`

---

## Related Capabilities

- [Clinical Documentation](02-clinical-documentation.md) - Medication lists
- [Patient Management](01-patient-management.md) - Pharmacy preferences
- [Clinical Decision Support](12-clinical-decision-support.md) - Drug interactions
- [Patient Portal](09-patient-portal.md) - Prescription access

---

## Compliance and Standards

- **EPCS** - Electronic Prescribing for Controlled Substances
- **DEA** - Drug Enforcement Administration requirements
- **SCRIPT** - NCPDP SCRIPT standard for e-prescribing
- **RxNorm** - Medication terminology standard
- **NDC** - National Drug Code
- **Meaningful Use** - eRx requirements
- **REMS** - Risk Evaluation and Mitigation Strategies

---

**End of Pharmacy and Prescription Management Capability**
