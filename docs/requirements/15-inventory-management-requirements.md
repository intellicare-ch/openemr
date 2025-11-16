# Inventory Management - Requirements Specification
**Capability ID:** 15 | **Version:** 1.0 | **Last Updated:** 2025-11-15

## Functional Requirements
### FR-IM-001: Drug Inventory Tracking
**Description:** System shall track medication inventory.
**Acceptance Criteria:** Drug catalog with NDC codes | Current stock levels | Inventory transactions (add, dispense, adjust) | Reorder points | Low stock alerts

### FR-IM-002: Lot Number Tracking
**Description:** System shall track lot numbers.
**Acceptance Criteria:** Lot number per inventory item | Expiration dates | Lot traceability in dispensing | Recall management | Lot-specific reporting

### FR-IM-003: Dispensing Documentation
**Description:** System shall record dispensing.
**Acceptance Criteria:** Patient dispensing recorded | Quantity deducted from inventory | Lot number captured | Provider documented | Dispensing receipt

### FR-IM-004: Controlled Substance Logging
**Description:** System shall comply with DEA requirements.
**Acceptance Criteria:** Controlled substance flag | Perpetual inventory count | Discrepancy investigation | Destruction documentation | DEA reporting

### FR-IM-005: Inventory Reports
**Description:** System shall provide inventory reports.
**Acceptance Criteria:** Current inventory levels | Transaction history | Sales by item | Destroyed drugs log | Expiring medications | Inventory valuation

## Non-Functional Requirements
### NFR-IM-001: Accuracy
**Description:** Inventory counts shall be accurate.
**Acceptance Criteria:** Physical inventory matches system quarterly | Discrepancy resolution process | Automatic alerts for large variances

### NFR-IM-002: Compliance - DEA
**Description:** Controlled substances shall comply with DEA.
**Acceptance Criteria:** Daily reconciliation | Perpetual inventory | Theft/loss reporting | Secure storage tracking

### NFR-IM-003: Auditability
**Description:** Inventory changes shall be logged.
**Acceptance Criteria:** All transactions logged with user, timestamp | Adjustment reasons documented | Audit trail for controlled substances

## Total Requirements: 14

**End of Inventory Management Requirements**
