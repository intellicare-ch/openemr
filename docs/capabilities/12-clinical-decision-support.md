# Clinical Decision Support

**Capability ID:** 12
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Clinical Decision Support (CDS) provides automated clinical alerts, reminders, quality measure tracking, and rule-based decision support improving care quality, patient safety, and regulatory compliance.

**Primary Purpose:** Support evidence-based clinical decision-making through automated alerts, preventive care reminders, quality measure tracking, and patient-specific clinical guidance.

**Primary Users:** Providers, nurses, quality improvement staff

---

## Key Features

### Clinical Decision Rules Engine
- Rule-based alert system
- Patient-specific reminders
- Preventive care tracking
- Chronic disease management protocols
- Evidence-based guidelines

**Files:** `/library/classes/rulesets/`
**Categories:** Prevention, chronic disease management, screening

### Clinical Reminders
- Automated patient reminders based on clinical data
- Overdue immunizations
- Cancer screenings (mammography, colonoscopy, etc.)
- Chronic disease monitoring (A1C, LDL, etc.)
- Medication management
- Alert display in patient chart
- Provider action tracking

**Files:** `/interface/patient_file/reminder/`
**Display:** Clinical reminders widget in patient summary

### Quality Measures (CQM)
- Clinical Quality Measure calculation
- NQF measure library (20+ measures)
- Automated numerator/denominator identification
- Population health dashboards
- Measure tracking and reporting

**Measures:** NQF-0002 (Depression), NQF-0013 (Hypertension), NQF-0028 (Tobacco), NQF-0038 (Immunization), NQF-0041 (Preventive Care), and more

**Files:** `/library/classes/rulesets/Cqm/`
**Reporting:** `/interface/reports/cqm.php`

### Automated Measure Calculation (AMC)
- Meaningful Use tracking
- CPOE usage
- eRx adoption
- Patient portal usage
- Health information exchange participation
- Clinical decision support usage

**Files:** `/library/classes/rulesets/Amc/`
**Reporting:** `/interface/reports/amc_tracking.php`

### Dated Reminders
- Provider task reminders
- Follow-up reminders
- Manual reminder creation
- Reminder calendar

**Files:** `/interface/main/dated_reminders/`

### Drug Interaction Checking
- Drug-drug interactions
- Drug-allergy checking
- Contraindication alerts
- Severity-based warnings

**Integration:** Via e-prescribing module or custom database

### Allergy Alerts
- Alert during medication prescribing
- Severity indicators
- Override with documentation

**Integration:** Prescription workflow

---

## Configuration

**Global Settings:**
- Enable/disable CDR
- Configure active rule sets
- Alert display settings
- Reminder frequency

**ACL Requirements:**
- View Reminders - clinical access
- Configure Rules - `admin/super`

---

## Related Capabilities

- [Clinical Documentation](02-clinical-documentation.md) - Clinical data drives rules
- [Pharmacy](06-pharmacy-prescriptions.md) - Drug interaction checking
- [Quality Measures](22-quality-measures.md) - CQM details
- [Reporting](07-reporting-analytics.md) - CQM reporting

---

## Compliance

- **Meaningful Use** - Clinical decision support requirement
- **ONC Certification** - CDS criteria
- **HEDIS** - Quality measure standards
- **NQF** - National Quality Forum measures

---

**End of Clinical Decision Support Capability**
