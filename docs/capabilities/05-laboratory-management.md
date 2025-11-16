# Laboratory Management

**Capability ID:** 05
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Laboratory Management provides comprehensive tools for ordering laboratory tests, managing lab compendiums, receiving and reviewing results, and integrating with external laboratory systems via HL7 interfaces.

**Primary Purpose:** Enable providers to order lab tests, track pending orders, receive electronic results, review and sign results, and make results available to patients while maintaining integration with external lab vendors.

**Primary Users:** Providers, nurses, medical assistants, lab coordinators

---

## Use Cases

1. **Lab Test Ordering** - Order laboratory procedures from catalog
2. **HL7 Order Transmission** - Send electronic orders to lab vendors
3. **Result Receipt** - Receive electronic lab results via HL7
4. **Result Review and Signature** - Provider review and sign-off on results
5. **Abnormal Result Flagging** - Identify and track out-of-range results
6. **Patient Result Access** - Make results available via patient portal
7. **Lab Compendium Management** - Maintain catalog of available tests
8. **Procedure Provider Management** - Configure lab vendor interfaces

---

## Key Features

### Procedure Ordering
- Search lab compendium by test name or code
- Select standard or custom lab panels  
- Enter clinical indication and diagnosis
- Route order to specific lab vendor
- Generate requisition forms
- Track order status (pending, in-progress, complete)

**Files:** `/interface/orders/procedure_order.php`, `/interface/forms/procedure_order/`
**Tables:** `procedure_order`, `procedure_order_code`, `procedure_type`

### HL7 Order Generation
- Generate HL7 ORM messages for electronic ordering
- Support vendor-specific HL7 formats (Quest, LabCorp, etc.)
- Include patient demographics, insurance, and clinical info
- Transmit via file, SFTP, or MLLP
- Track transmission status

**Files:** `/interface/orders/gen_hl7_order.inc.php`
**Library:** aranyasen/hl7

### HL7 Result Receipt
- Receive HL7 ORU messages with lab results
- Parse result data (test, value, units, reference range)
- Match results to pending orders
- Handle multiple result formats (numeric, text, coded)
- Store result attachments (PDFs, images)
- Auto-import to correct patient chart

**Files:** `/interface/orders/receive_hl7_results.inc.php`
**Tables:** `procedure_result`, `procedure_report`

### Result Review Workflow
- Display pending results requiring review
- Show abnormal flags (high, low, critical)
- Provider review and electronic signature
- Add comments/interpretation
- Order follow-up tests
- Track result status (pending review, reviewed, amended)

**Files:** `/interface/orders/orders_results.php`, `/interface/patient_file/summary/labdata.php`

### Lab Compendium
- Import standardized test catalogs from vendors
- Maintain in-house lab test directory
- Configure test codes (LOINC), CPT codes for billing
- Set normal reference ranges
- Define specimen requirements
- Map to FHIR Observation codes

**Files:** `/interface/orders/types.php`, `/interface/orders/load_compendium.php`

---

## Configuration

**Global Settings:**
- Lab Interface Configuration - vendor-specific settings
- HL7 Version - 2.3, 2.5, 2.5.1
- Result Auto-Import - automatic vs manual review
- LOINC Code Usage - standardized observation codes
- Reference Ranges - normal value ranges by age/sex

**ACL Requirements:**
- Order Labs - `patients/lab` write
- Review Results - `patients/lab` review
- Manage Compendium - `admin/super`

---

## Related Capabilities

- [Clinical Documentation](02-clinical-documentation.md) - Results incorporated in chart
- [Billing](04-billing-revenue-cycle.md) - Lab billing and coding
- [Patient Portal](09-patient-portal.md) - Patient result access
- [HIE](08-health-information-exchange.md) - FHIR DiagnosticReport resources

---

## Compliance and Standards

- **HL7 v2.x** - ORM (orders), ORU (results)
- **LOINC** - Standardized lab test codes
- **FHIR R4** - DiagnosticReport, Observation, Specimen, ServiceRequest resources
- **CLIA** - Clinical Laboratory Improvement Amendments
- **HIPAA** - Secure result transmission

---

**End of Laboratory Management Capability**
