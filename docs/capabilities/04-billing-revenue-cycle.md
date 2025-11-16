# Billing and Revenue Cycle Management

**Capability ID:** 04
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Billing and Revenue Cycle Management provides comprehensive functionality for medical billing, insurance claims, payment processing, and accounts receivable management supporting both professional (CMS-1500) and institutional (UB-04) billing workflows.

**Primary Purpose:** Enable complete revenue cycle management from charge capture through claim submission, payment posting, and collections while maintaining compliance with healthcare billing regulations and payer requirements.

**Primary Users:** Billing staff, coders, front desk, practice managers, providers

---

## Use Cases

1. **Encounter Billing (Fee Sheet)** - Capture charges during patient visit
2. **Claims Generation** - Create and submit insurance claims (837P/837I)
3. **Eligibility Verification** - Check patient insurance eligibility
4. **Electronic Claims Submission** - Submit claims via EDI to clearinghouses
5. **ERA Processing** - Post electronic remittance advice payments
6. **Payment Posting** - Record patient and insurance payments
7. **Patient Statements** - Generate and send patient billing statements
8. **Collections Management** - Track and manage outstanding balances
9. **Denial Management** - Track and resubmit denied claims
10. **Reporting** - Generate financial and billing reports

---

## Features

### Feature 4.1: Fee Sheet (Encounter Billing)

**Description:**
Point-of-care charge capture allowing providers or billing staff to document procedures performed and diagnoses assigned during patient encounter with real-time CPT/ICD code lookup and fee schedule pricing.

**How to Use (User Instructions):**
1. Open patient encounter
2. Navigate to Fee Sheet or Billing tab
3. Add diagnosis codes (ICD-10):
   - Search by code or description
   - Select appropriate diagnosis
   - Set as primary diagnosis if applicable
4. Add procedure codes (CPT/HCPCS):
   - Search by code or description
   - Select procedure
   - Set quantity (units)
   - Add modifiers if applicable
   - Link to diagnosis (pointer)
   - Fee auto-populates from fee schedule
5. Add additional procedures as needed
6. Review total charges
7. Document justification codes if required
8. Click "Save" to post charges
9. Charges appear in billing queue

**How to Test:**
- **Test 1:** Add diagnosis code by search, verify saved
- **Test 2:** Add CPT code, verify fee auto-fills from schedule
- **Test 3:** Add modifier to CPT, verify modifier saved
- **Test 4:** Link multiple CPT codes to same diagnosis
- **Test 5:** Set units >1, verify charge multiplied correctly
- **Test 6:** Save fee sheet, verify appears in billing report
- **Test 7:** Test with different insurance (fee should match insurance fee schedule)
- **Test 8:** Test required field validation

**Expected System Behavior:**
- Charges stored in `billing` table
- Each CPT code creates separate billing row
- ICD-10 codes stored and linked via diagnosis pointer (1-12)
- Modifiers stored in modifier field (up to 4 modifiers)
- Fee pulled from `codes` table based on insurance-specific fee schedule
- Units multiply base fee
- Authorization numbers tracked if required
- Charges linked to encounter via encounter ID
- Provider defaults from encounter provider
- Facility determines billing facility for claim
- Audit log entry created
- ACL enforced (billing write permission required)

**Technical Details:**
- **Location:** `/interface/forms/fee_sheet/`
- **Superbill:** `/interface/patient_file/encounter/superbill_codes.php`
- **Database Table:** `billing`
- **Code Tables:** `codes` (CPT), `icd10_dx_order_code` (ICD-10)
- **Fee Schedule:** Multiple fee schedules supported per insurance

---

### Feature 4.2: Claims Generation (CMS-1500 / 837P)

**Description:**
Generate professional insurance claims in CMS-1500 paper format or electronic 837P format for submission to insurance payers including all required fields, diagnosis/procedure linking, and payer-specific requirements.

**How to Use (User Instructions):**
1. Navigate to Billing → Billing Manager or Billing Report
2. Select encounters ready to bill:
   - Filter by date range
   - Filter by provider, facility, insurance
   - Filter by billing status (unbilled)
3. Review charges for accuracy
4. Select encounters to include in batch
5. Click "Generate Claims" or "Create X12"
6. Select output format:
   - Electronic (837P file for EDI transmission)
   - Paper (CMS-1500 PDF for printing/mailing)
7. For electronic:
   - System generates X12 837P file
   - File ready for upload to clearinghouse
8. For paper:
   - System generates PDF of CMS-1500 forms
   - Print and mail to payers
9. Claims marked as "Billed" with batch number

**How to Test:**
- **Test 1:** Generate single claim, verify all required fields populated
- **Test 2:** Generate batch of claims, verify all included
- **Test 3:** Test 837P file format validation (X12 syntax)
- **Test 4:** Test CMS-1500 PDF generation and formatting
- **Test 5:** Verify diagnosis pointers correct
- **Test 6:** Test claim with secondary insurance (COB)
- **Test 7:** Verify NPI numbers included for provider and facility
- **Test 8:** Test payer-specific requirements (different formats for different payers)
- **Test 9:** Verify claims marked as billed after generation

**Expected System Behavior:**
- System validates all required fields before generation:
  - Patient demographics complete
  - Insurance policy information
  - Provider NPI
  - Facility NPI
  - Valid CPT and ICD-10 codes
  - Appropriate modifiers
- 837P file generated following X12 5010 standards
- Claim number (ICN) assigned to each claim
- Batch number assigned for tracking
- Claims record created in `claims` table
- Billing rows updated with claim linkage
- Status changed from "unbilled" to "billed"
- Electronic file ready for SFTP upload or manual submission
- PDF follows CMS-1500 (02/12) format requirements
- Clearinghouse-specific formatting applied if configured
- Audit log entry created

**Technical Details:**
- **Primary Files:** `/interface/billing/billing_process.php`, `/interface/billing/billing_report.php`
- **X12 Generation:** `/library/billing.inc.php`, custom X12 builder
- **Database Tables:** `billing`, `claims`, `form_encounter`, `insurance_data`
- **Standards:** X12 5010 837P, CMS-1500 (02/12)
- **Output:** X12 text file or PDF

---

### Feature 4.3: Insurance Eligibility Verification (270/271)

**Description:**
Electronic insurance eligibility and benefits verification using X12 270 transactions with automated 271 response parsing to confirm patient coverage, copays, deductibles, and benefits before service delivery.

**How to Use (User Instructions):**
1. Navigate to Billing → EDI 270 Eligibility
2. Select patient
3. Select insurance (primary, secondary, or tertiary)
4. Click "Check Eligibility"
5. System generates 270 inquiry transaction
6. Submit to clearinghouse or payer
7. Receive 271 response
8. System parses and displays:
   - Coverage status (active/inactive)
   - Effective dates
   - Copay amounts
   - Deductible (met/remaining)
   - Out-of-pocket maximum
   - Coverage limitations
9. Save eligibility information to patient record
10. Use in encounter billing

**How to Test:**
- **Test 1:** Check eligibility for active insurance, verify response received
- **Test 2:** Check eligibility for inactive insurance, verify inactive status shown
- **Test 3:** Verify copay amount displayed correctly
- **Test 4:** Verify deductible information parsed correctly
- **Test 5:** Test batch eligibility checking for multiple patients
- **Test 6:** Test storage of eligibility response for documentation
- **Test 7:** Test error handling for rejected requests

**Expected System Behavior:**
- 270 transaction generated following X12 5010 standards
- Required fields: Subscriber ID, patient DOB, provider NPI
- Transmission to clearinghouse via configured method
- 271 response received and stored
- Response parsed extracting:
  - Eligibility status codes
  - Date ranges (coverage periods)
  - Benefit amounts (copay, deductible, OOP max)
  - Service type-specific benefits
- Results displayed in user-friendly format
- Eligibility info can be saved to patient insurance record
- Historical eligibility checks retained for audit
- ACL enforced
- Audit logging

**Technical Details:**
- **Primary Files:** `/interface/billing/edi_270.php`, `/interface/billing/edi_271.php`
- **X12 Handling:** `/library/edihistory/` parsers
- **Database Tables:** EDI history tables for 270/271 storage
- **Standards:** X12 5010 270/271
- **Transmission:** Clearinghouse SFTP or direct payer connection

---

### Feature 4.4: Electronic Remittance Advice (ERA) Posting (835)

**Description:**
Automated posting of insurance payments and adjustments from electronic remittance advice (ERA) 835 transactions reducing manual data entry and improving payment posting accuracy.

**How to Test:**
- **Test 1:** Upload 835 ERA file, verify parsed successfully
- **Test 2:** Auto-post ERA, verify payments applied to correct claims
- **Test 3:** Verify adjustments posted correctly
- **Test 4:** Verify denial codes captured
- **Test 5:** Test partial payment scenarios
- **Test 6:** Test patient responsibility calculation
- **Test 7:** Manual review mode for uncertain matches
- **Test 8:** Batch posting of multiple ERAs

**Expected System Behavior:**
- 835 file uploaded or received from clearinghouse
- X12 835 transaction parsed extracting:
  - Payer information
  - Check/EFT number and amount
  - Claim-level adjustments
  - Service line payments and adjustments
  - Reason codes (CAS, CARC, RARC)
- System matches ERA to existing claims by ICN or patient/date
- Payments posted to `ar_activity` table
- Payment session created for batch tracking
- Adjustments applied (contractual, PR, CO)
- Patient responsibility calculated (claim balance minus payment minus adjustments)
- Denial codes recorded for denied charges
- Unmatched ERAs flagged for manual review
- Payment posting audit trail maintained
- Reports available showing posted ERA activity

**Technical Details:**
- **Primary Files:** `/interface/billing/sl_eob_search.php`, `/interface/billing/sl_eob_process.php`, `/interface/billing/era_payments.php`
- **835 Parser:** `/library/edihistory/edih_835_*` classes
- **Database Tables:** `ar_activity`, `ar_session`, `claims`
- **Standards:** X12 5010 835
- **Automation:** Configurable auto-posting rules

---

### Feature 4.5: Payment Posting (Manual)

**Description:**
Manual entry of patient and insurance payments, adjustments, and transfers with multi-invoice payment allocation and payment method tracking.

**How to Use (User Instructions):**
1. Navigate to Billing → Payments (or New Payment)
2. Select payment source:
   - Insurance payment (EOB entry)
   - Patient payment
3. Enter payment header information:
   - Payment date
   - Payment amount
   - Payment method (check, cash, credit card, EFT)
   - Check/reference number
4. Select patient
5. Display patient invoices/charges
6. Allocate payment to specific charges:
   - Select charge line items
   - Enter payment amount per item
   - Enter adjustments (write-off, contractual)
   - Enter reason codes
7. System calculates remaining balance
8. If overpayment, record as credit
9. Save payment
10. Payment posted and balance updated
11. Print receipt if patient payment

**How to Test:**
- **Test 1:** Post patient payment, verify applied to correct encounter
- **Test 2:** Post insurance payment with adjustment, verify both recorded
- **Test 3:** Post payment greater than balance, verify overpayment handled
- **Test 4:** Split payment across multiple encounters
- **Test 5:** Test credit card processing integration if enabled
- **Test 6:** Void payment, verify reversal correct
- **Test 7:** Print receipt, verify details accurate
- **Test 8:** Test batch payment posting

**Expected System Behavior:**
- Payment creates `ar_session` record (payment session)
- Each line item creates `ar_activity` record
- Payment types: insurance, patient, adjustment, transfer
- Payment methods tracked (cash, check, credit card, EFT)
- Balance recalculated: original charge - payments - adjustments
- Zero balance claims marked as paid/closed
- Patient responsibility (copay, deductible, coinsurance) tracked separately from insurance
- Overpayments create credit balance (negative)
- Refunds processed for credit balances if needed
- Receipt generation for patient payments
- Payment integration with credit card gateways (Stripe, Authorize.net)
- ACL enforced (payment posting permission)
- Comprehensive audit logging

**Technical Details:**
- **Primary Files:** `/interface/billing/new_payment.php`, `/interface/patient_file/front_payment.php`
- **Database Tables:** `ar_session`, `ar_activity`, `billing`
- **Payment Gateway:** Stripe, Authorize.net integrations
- **Receipt:** PDF generation for patient receipts

---

### Feature 4.6: Patient Statements

**Description:**
Generate and print patient billing statements showing charges, payments, adjustments, and current balance with customizable formatting and messaging.

**How to Use (User Instructions):**
1. Navigate to Billing → Statements
2. Select statement criteria:
   - Date range
   - Provider, facility
   - Balance threshold (e.g., >$10)
   - Insurance status (patient responsibility only)
3. Click "Generate Statements"
4. Review statement batch
5. Print or email statements to patients
6. Mark statements as sent
7. Track statement history

**How to Test:**
- **Test 1:** Generate statements for all patients with balance >$0
- **Test 2:** Verify statement shows all unbilled encounters
- **Test 3:** Test statement formatting and branding
- **Test 4:** Email statement to patient, verify delivery
- **Test 5:** Test aging categories (30/60/90 days)
- **Test 6:** Verify statement includes payment instructions
- **Test 7:** Test batch printing
- **Test 8:** Verify collection messages displayed correctly

**Expected System Behavior:**
- Statements query `billing` and `ar_activity` to calculate patient balance
- Only patient responsibility shown (insurance balance excluded)
- Statement includes:
  - Patient demographics
  - Itemized charges by date
  - Payments and adjustments
  - Current balance by age (current, 30, 60, 90+ days)
  - Payment instructions
  - Practice contact information
- PDF generation for printing or email
- Email delivery via PHPMailer
- Statement history tracked (date sent)
- Customizable statement message (collection language)
- Can suppress small balances
- HIPAA-compliant formatting (minimal clinical detail)

**Technical Details:**
- **Primary Files:** `/interface/billing/billing_tracker.php` (statement generation)
- **Database Query:** Complex query joining billing, ar_activity, patient_data
- **PDF Generation:** DomPDF or mPDF
- **Email:** PHPMailer integration

---

### Feature 4.7: Collections Management

**Description:**
Track and manage outstanding patient balances with aging reports, collection letters, and payment plan management.

**How to Use (User Instructions):**
1. Navigate to Reports → Collections Report
2. View accounts receivable aging:
   - Current (0-30 days)
   - 31-60 days
   - 61-90 days
   - 91+ days
3. Sort by patient, amount, or age
4. Select accounts for collection action:
   - Send statement
   - Send collection letter
   - Assign to collection agency
   - Set up payment plan
   - Write off bad debt
5. Document collection activity
6. Track collection status

**How to Test:**
- **Test 1:** Generate A/R aging report, verify amounts correct
- **Test 2:** Filter by age category, verify calculation
- **Test 3:** Send collection letter, verify sent
- **Test 4:** Set up payment plan, verify installments tracked
- **Test 5:** Write off bad debt, verify adjustment posted
- **Test 6:** Test collection agency export
- **Test 7:** Track collection calls and notes

**Expected System Behavior:**
- A/R aging calculated from invoice date to current date
- Balance = Charges - Payments - Adjustments
- Aging categories: 0-30, 31-60, 61-90, 91+ days
- Collection letters customizable by age/amount
- Payment plans create scheduled payment records
- Bad debt write-off creates adjustment entry
- Collection agency export generates patient list
- Collection activity logged for compliance
- FDCPA compliance considerations (Fair Debt Collection Practices Act)
- ACL enforced

**Technical Details:**
- **Primary Files:** `/interface/reports/collections_report.php`
- **Database Query:** Aggregate billing and ar_activity with date calculations
- **Letters:** Template-based collection letter generation
- **Payment Plans:** Custom payment schedule tracking

---

### Feature 4.8: UB-04 Institutional Billing

**Description:**
Generate institutional claims using UB-04 format and 837I electronic standard for hospital outpatient, skilled nursing, home health, and other institutional services.

**How to Use (User Instructions):**
1. Document institutional encounter with required fields:
   - Admission date, discharge date
   - Admission type, source
   - Patient status (discharge status)
   - Revenue codes (instead of CPT)
   - Occurrence codes and dates
   - Value codes
2. Navigate to Billing → UB-04
3. Select encounters for UB-04 billing
4. Generate claim in UB-04 format
5. Submit electronically (837I) or print paper UB-04
6. Track institutional claims separately

**How to Test:**
- **Test 1:** Create institutional encounter with revenue codes
- **Test 2:** Generate 837I file, verify format valid
- **Test 3:** Generate UB-04 PDF, verify form layout correct
- **Test 4:** Test occurrence codes and dates
- **Test 5:** Test value codes
- **Test 6:** Verify institutional-specific fields populated
- **Test 7:** Test different bill types (inpatient, outpatient, SNF)

**Expected System Behavior:**
- UB-04 data stored in specialized institutional fields
- Revenue codes used instead of CPT codes
- 837I transaction follows X12 5010 institutional standard
- Form locators populated correctly on UB-04
- Bill type codes (3-digit) indicate type of facility and care
- Occurrence codes document significant events (admission, accident, etc.)
- Value codes document amounts (covered days, Medicare lifetime reserve)
- Institutional claims tracked separately from professional
- Different payment rules apply
- ACL enforced

**Technical Details:**
- **Primary Files:** `/interface/billing/ub04_*.php`
- **Database Tables:** `billing`, `form_encounter` (institutional fields)
- **Standards:** X12 5010 837I, UB-04 form
- **Revenue Codes:** NUBC revenue code table

---

## Configuration

### Global Settings

**Location:** Administration → Globals → Billing / EDI

- **X12 Partner** - Clearinghouse configuration
- **Electronic Billing** - Enable EDI transactions
- **Payer IDs** - Insurance payer identifiers
- **NPI Numbers** - Provider and facility NPIs
- **Fee Schedules** - Multiple fee schedules per insurance type
- **Billing Codes** - CPT, HCPCS code sets
- **Modifiers** - Approved modifier list
- **Claim Numbering** - ICN format
- **Statement Settings** - Statement message, thresholds
- **Payment Methods** - Accepted payment types
- **Credit Card Processing** - Gateway configuration

### ACL Requirements

- **Billing View** - `acct/bill` - View billing information
- **Billing Create** - `acct/bill/write` - Create/edit charges
- **Payment Post** - `acct/rep` - Post payments
- **Claims Submit** - `acct/eob` - Submit claims
- **Financial Reports** - `acct/rep` - View financial reports

---

## Related Capabilities

- **[Clinical Documentation](02-clinical-documentation.md)** - Encounter documentation drives billing
- **[Patient Management](01-patient-management.md)** - Insurance information
- **[Appointment Scheduling](03-appointment-scheduling.md)** - Encounter creation from appointments
- **[Reporting and Analytics](07-reporting-analytics.md)** - Financial reports
- **[Health Information Exchange](08-health-information-exchange.md)** - Claims via EDI

---

## Compliance and Standards

- **HIPAA 5010** - X12 transaction standards (837, 835, 270, 271)
- **CMS-1500** - Professional claim form (02/12 version)
- **UB-04** - Institutional claim form
- **NPI** - National Provider Identifier required
- **ICD-10-CM** - Diagnosis coding standard
- **CPT/HCPCS** - Procedure coding standards
- **NUBC** - National Uniform Billing Committee standards
- **FDCPA** - Fair Debt Collection Practices Act

---

**End of Billing and Revenue Cycle Management Capability**
