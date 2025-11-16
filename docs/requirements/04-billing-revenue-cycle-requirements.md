# Billing and Revenue Cycle Management - Requirements Specification

**Capability ID:** 04
**Version:** 1.0
**Last Updated:** 2025-11-15

---

## Functional Requirements

### FR-BR-001: Fee Sheet Charge Capture
**Description:** The system shall allow charge capture during patient encounter with CPT/ICD code selection and fee calculation.

**Acceptance Criteria:**
- System supports adding multiple CPT/HCPCS procedure codes per encounter
- System supports adding multiple ICD-10 diagnosis codes per encounter
- Fee auto-populates from fee schedule based on insurance type
- Modifiers (up to 4) addable to procedure codes
- Units multiplicable for repeated procedures
- Diagnosis pointers (1-12) linkable to procedures
- Authorization numbers trackable
- Charges immediately available in billing queue
- Audit log created for charge entry

### FR-BR-002: Claims Generation (837P)
**Description:** The system shall generate professional insurance claims in CMS-1500 and 837P electronic format.

**Acceptance Criteria:**
- System validates all required fields before claim generation
- 837P file conforms to X12 5010 standard
- CMS-1500 PDF matches required format (02/12 version)
- Batch claim generation supported
- Individual claim generation supported
- Claim number (ICN) assigned uniquely
- Claims marked as "billed" with batch number
- Payer-specific formatting applied
- Claims record created in database
- Electronic file ready for clearinghouse transmission

### FR-BR-003: Eligibility Verification (270/271)
**Description:** The system shall check insurance eligibility electronically and parse responses.

**Acceptance Criteria:**
- 270 transaction generated with subscriber ID, DOB, provider NPI
- Transaction transmitted to clearinghouse or payer
- 271 response received and parsed
- Eligibility status displayed (active/inactive)
- Coverage details extracted (copay, deductible, OOP max)
- Benefit information by service type displayed
- Results savable to patient insurance record
- Historical eligibility checks retained
- Batch eligibility checking supported

### FR-BR-004: ERA Processing (835)
**Description:** The system shall process electronic remittance advice and auto-post payments.

**Acceptance Criteria:**
- 835 file uploadable or received from clearinghouse
- Transaction parsed extracting payment and adjustment details
- Claims matched by ICN or patient/date
- Payments auto-posted to correct charges
- Adjustments applied (contractual, patient responsibility)
- Denial codes captured and displayed
- Unmatched ERAs flagged for manual review
- Payment session created for tracking
- Posting audit trail maintained

### FR-BR-005: Manual Payment Posting
**Description:** The system shall allow manual entry of insurance and patient payments with allocation.

**Acceptance Criteria:**
- Payment date, amount, method (check, cash, card, EFT) entered
- Payment allocatable to multiple invoices/charges
- Adjustments enterable (write-off, contractual, transfer)
- Overpayment creates credit balance
- Receipt printable for patient payments
- Credit card processing integration supported
- Payment reversal/void supported
- Audit trail for all payment transactions

### FR-BR-006: Patient Statements
**Description:** The system shall generate patient billing statements showing charges, payments, and balances.

**Acceptance Criteria:**
- Statement shows itemized charges by date
- Payments and adjustments displayed
- Current balance calculated
- Aging categories shown (30/60/90+ days)
- Payment instructions included
- Practice branding/logo supported
- PDF generation for printing or email
- Email delivery to patient
- Statement history tracked (date sent)
- Small balance suppression configurable

### FR-BR-007: Collections Management
**Description:** The system shall provide accounts receivable aging and collections tracking.

**Acceptance Criteria:**
- A/R aging report with categories: 0-30, 31-60, 61-90, 91+ days
- Sort by patient, amount, or age
- Collection letter generation
- Payment plan creation and tracking
- Bad debt write-off capability
- Collection agency export
- Collection activity notes
- Compliance with FDCPA (Fair Debt Collection Practices Act)

### FR-BR-008: UB-04 Institutional Billing
**Description:** The system shall generate institutional claims using UB-04 format and 837I electronic standard.

**Acceptance Criteria:**
- Revenue codes used instead of CPT codes
- Institutional-specific fields supported (admission date, discharge date, bill type, occurrence codes, value codes)
- 837I file conforms to X12 5010 institutional standard
- UB-04 PDF form generated
- Different payment rules from professional billing
- Institutional claims tracked separately

### FR-BR-009: Fee Schedule Management
**Description:** The system shall maintain multiple fee schedules for different insurance types.

**Acceptance Criteria:**
- Multiple fee schedules per CPT code
- Fee schedule assignable to insurance company
- Default fee schedule configurable
- Fee amounts editable per schedule
- Effective dates for fee changes
- Fee schedule comparison reporting
- Import/export fee schedules

### FR-BR-010: Billing Reports
**Description:** The system shall provide comprehensive billing and financial reports.

**Acceptance Criteria:**
- Collections report (A/R aging)
- Payment activity reports (receipts by method, by day)
- Provider productivity reports
- Insurance payment analysis
- Denial reports
- Day sheets (provider, facility, payment)
- Custom date range selection
- Export to CSV/Excel

---

## Non-Functional Requirements

### NFR-BR-001: Performance - Claim Generation
**Description:** Claims generation shall complete efficiently for batch and individual processing.

**Acceptance Criteria:**
- Single claim generates within 2 seconds
- Batch of 100 claims generates within 60 seconds
- 837P file creation completes within 5 minutes for 500 claims
- ERA processing of 100 claims completes within 120 seconds
- Database optimized for billing queries

### NFR-BR-002: Accuracy - Billing Calculations
**Description:** Billing calculations shall be mathematically accurate.

**Acceptance Criteria:**
- Fee calculations accurate to 2 decimal places
- Unit multiplication accurate
- Payment allocation totals match payment amount (no rounding errors)
- Balance calculations accurate (charges - payments - adjustments)
- Aging calculations based on correct invoice dates
- Tax calculations accurate (if applicable)

### NFR-BR-003: Security - Financial Data Protection
**Description:** Billing and financial data shall be protected with access controls and encryption.

**Acceptance Criteria:**
- ACL enforced for billing access (view, create, post payments)
- Payment transactions logged with user, timestamp, IP
- TLS 1.2+ required for transmission
- Credit card data PCI-DSS compliant (tokenization, no storage of CVV)
- Financial reports restricted to authorized users
- ERA files encrypted at rest

### NFR-BR-004: Compliance - HIPAA 5010
**Description:** EDI transactions shall conform to HIPAA 5010 standards.

**Acceptance Criteria:**
- 837P/837I conform to X12 5010 version 007020
- 270/271 eligibility transactions conform to X12 5010
- 835 ERA parsing supports X12 5010
- Transaction validation before transmission
- Compliance testing with clearinghouse
- Rejection handling and resubmission

### NFR-BR-005: Compliance - Billing Regulations
**Description:** Billing processes shall comply with healthcare billing regulations.

**Acceptance Criteria:**
- NPI numbers required for all providers and facilities
- Taxonomy codes correctly assigned
- Place of Service codes compliant
- Modifier usage follows CMS guidelines
- Timely filing tracked (claims submitted within payer deadlines)
- Coordination of Benefits (COB) rules followed

### NFR-BR-006: Reliability - Payment Posting
**Description:** Payment posting shall ensure data integrity and prevent errors.

**Acceptance Criteria:**
- Database transactions ensure atomic operations
- Payment reversal fully reverses all changes
- Duplicate payment detection
- Payment allocation cannot exceed payment amount
- Concurrent payment posting supported without conflicts
- Backup and recovery tested for financial data

### NFR-BR-007: Auditability - Financial Audit Trail
**Description:** All billing and payment activities shall be comprehensively logged.

**Acceptance Criteria:**
- Charge entry logged with user, timestamp
- Claims submission logged
- Payment posting logged with details
- Adjustments and write-offs logged with reason
- ERA processing logged
- Audit logs retained minimum 7 years
- Tamper-proof audit logs

### NFR-BR-008: Usability - Billing Workflow
**Description:** Billing interface shall support efficient revenue cycle operations.

**Acceptance Criteria:**
- Fee sheet completable in under 2 minutes
- Payment posting form intuitive
- Claim status easily viewable
- Error messages clear and actionable
- Keyboard shortcuts for common tasks
- Batch operations for repetitive tasks
- Undo functionality for mistakes

### NFR-BR-009: Integration - Clearinghouse
**Description:** System shall integrate seamlessly with clearinghouses for EDI transmission.

**Acceptance Criteria:**
- SFTP transmission supported
- Multiple clearinghouse configurations
- Transmission status tracking
- Acknowledgment (997) processing
- Error report parsing
- Retry logic for failed transmissions
- Secure credential storage

### NFR-BR-010: Reporting - Financial Analytics
**Description:** Financial reports shall provide actionable business intelligence.

**Acceptance Criteria:**
- Reports accurate and reconcilable
- Real-time data (not significantly delayed)
- Drill-down capability to transaction detail
- Export to Excel for further analysis
- Scheduled report generation and email
- Dashboard with key metrics (A/R, collection rate, denial rate)

---

## Data Quality Requirements

### DQ-BR-001: Coding Accuracy
**Description:** CPT and ICD codes shall be current and valid.

**Acceptance Criteria:**
- Code sets updated annually (ICD-10, CPT)
- Invalid codes flagged at entry
- Deprecated codes identified
- Code search returns only current codes
- Code descriptions accurate

### DQ-BR-002: Fee Schedule Currency
**Description:** Fee schedules shall be current and competitive.

**Acceptance Criteria:**
- Fee schedules reviewed annually
- Medicare fee schedule updated annually
- Commercial payer fee schedules current
- Fee amount changes logged
- Historical fee schedules retained

### DQ-BR-003: Claims Accuracy
**Description:** Claims shall have accurate and complete information.

**Acceptance Criteria:**
- Claim error rate <5% (rejected/total submitted)
- Required fields 100% complete
- NPI numbers valid
- Diagnosis codes valid for date of service
- Procedure codes valid for date of service
- Clean claim rate >90%

---

## Integration Requirements

### IR-BR-001: Practice Management System
**Description:** Billing shall integrate with all clinical and administrative systems.

**Acceptance Criteria:**
- Encounter data flows to billing automatically
- Patient demographics auto-populate claims
- Insurance information current on claims
- Provider information current on claims
- Facility information current on claims

### IR-BR-002: Payment Gateway Integration
**Description:** Credit card payments shall integrate with payment processors.

**Acceptance Criteria:**
- Stripe integration supported
- Authorize.net integration supported
- PCI-DSS compliant (no card data stored)
- Tokenization for recurring payments
- Payment confirmation immediate
- Failed payment handling and retry
- Refund processing supported

### IR-BR-003: Bank Integration
**Description:** Payment posting shall support bank reconciliation.

**Acceptance Criteria:**
- Payment batch totals exportable
- Deposit summaries match bank deposits
- ACH/EFT payment matching
- Bank reconciliation reporting
- Lockbox processing supported

---

## Total Requirements: 32
- Functional Requirements: 10
- Non-Functional Requirements: 10
- Data Quality Requirements: 3
- Integration Requirements: 3
- Additional billing requirements: 6

---

**End of Billing and Revenue Cycle Management Requirements**
