# Messaging and Communication

**Capability ID:** 11
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Messaging and Communication provides multi-channel communication including internal staff messaging, patient portal messaging, batch email/SMS notifications, Direct secure messaging, fax integration, and appointment reminders.

**Primary Purpose:** Facilitate communication between staff, providers, and patients through secure, HIPAA-compliant channels supporting clinical coordination, patient engagement, and administrative notifications.

**Primary Users:** Providers, nurses, staff, administrators, patients

---

## Key Features

### Internal Provider Messaging
- Staff-to-staff secure messaging
- Lab result notifications
- Task assignments
- Message threads
- Priority flagging
- Read receipts

**Files:** `/interface/main/messages/`
**Tables:** Custom messaging tables

### Patient Portal Messaging
- Patient-to-provider secure messaging
- Provider responses
- Attachment support
- Message threads
- Real-time chat option

**Files:** `/portal/messaging/`
**Security:** Encrypted, authenticated access

### Batch Communications
- Email notifications (bulk)
- SMS messaging (bulk)
- Phone call reminders
- Appointment reminders
- Patient recalls
- Custom message templates

**Files:** `/interface/batchcom/`
**Modules:** `/modules/sms_email_reminder/`
**Integrations:** Twilio, Clickatell, RingCentral

### Direct Secure Messaging
- Provider-to-provider encrypted messaging
- HISP integration
- S/MIME encryption
- CCDA attachments
- Health information exchange

**Files:** `/library/direct_message_check.inc.php`
**Standards:** Direct Trust Protocol

### Appointment Reminders (MedEx)
- Automated appointment reminders
- Email and SMS delivery
- Patient confirmations
- Cancellation requests
- Recall campaigns

**Module:** `/library/MedEx/`
**Service:** MedEx background service

### Fax Integration
- Send faxes from system
- Receive faxes
- Fax queue management
- Patient fax dispatch

**Files:** `/interface/fax/`
**Module:** `/interface/modules/custom_modules/oe-module-faxsms/`

### Office Notes
- Internal office communication
- Bulletin board
- Staff announcements

**Files:** `/interface/main/onotes/`

---

## Configuration

**Global Settings:**
- Email Server (SMTP)
- SMS Provider (Twilio, etc.)
- Direct Messaging (HISP)
- Fax Provider
- Reminder Settings

**ACL Requirements:**
- Messaging - various based on message type
- Batch Communications - `admin/super` or configured role

---

## Related Capabilities

- [Patient Portal](09-patient-portal.md) - Patient messaging
- [Appointment Scheduling](03-appointment-scheduling.md) - Reminders
- [Health Information Exchange](08-health-information-exchange.md) - Direct messaging

---

## Compliance

- **HIPAA** - Secure messaging, encryption
- **Direct Protocol** - Secure health information exchange

---

**End of Messaging and Communication Capability**
