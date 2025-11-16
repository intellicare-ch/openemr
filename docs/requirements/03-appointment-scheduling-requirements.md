# Appointment Scheduling - Requirements Specification

**Capability ID:** 03
**Version:** 1.0
**Last Updated:** 2025-11-15

---

## Functional Requirements

### FR-AS-001: Individual Appointment Creation
**Description:** The system shall allow authorized users to create individual patient appointments with specified providers, facilities, dates, times, and durations.

**Acceptance Criteria:**
- System creates appointment with required fields: patient, provider, date, time, duration, facility
- System allows appointment category selection (office visit, procedure, follow-up, etc.)
- System validates patient exists before allowing appointment creation
- System validates provider exists and is active
- System records appointment reason/chief complaint
- System assigns unique appointment ID
- System allows comments/notes on appointment
- Appointment immediately visible on calendar after creation
- Audit log created for appointment creation

### FR-AS-002: Multi-Provider Calendar View
**Description:** The system shall display calendars for single or multiple providers simultaneously with configurable view modes.

**Acceptance Criteria:**
- Day view shows hourly time slots for selected date
- Week view shows 7-day overview
- Month view shows monthly calendar with appointment counts
- User selects one or more providers to display
- Appointments color-coded by category
- Double-click time slot creates new appointment
- Click existing appointment opens details
- Calendar refresh updates without full page reload
- Print functionality for calendar views

### FR-AS-003: Appointment Status Management
**Description:** The system shall track appointment lifecycle status from scheduled through completion.

**Acceptance Criteria:**
- Status options include: Scheduled, Confirmed, Arrived, In Progress, Completed, Cancelled, No-Show, Rescheduled
- Status updates reflected immediately on calendar and reports
- Cancelled appointments visually distinguished (strikethrough or hidden)
- No-show status triggers workflow actions (patient notification, follow-up task)
- Completed appointments optionally auto-create encounters
- Status transitions logged in audit trail
- Reports available for no-show rates, confirmation rates

### FR-AS-004: Recurring Appointment Series
**Description:** The system shall support creation of recurring appointment series with configurable patterns.

**Acceptance Criteria:**
- Recurrence patterns: Daily, Weekly, Monthly, Yearly
- Interval specification (every N days/weeks/months)
- Day of week selection for weekly recurrence
- End condition: number of occurrences OR end date
- All occurrences created immediately and visible on calendar
- Individual occurrences editable independently
- Single occurrence cancellable without affecting series
- Entire series deletable with confirmation
- Holiday skip option (configurable)

### FR-AS-005: Group Appointment Scheduling
**Description:** The system shall support scheduling group appointments with multiple patients attending single session.

**Acceptance Criteria:**
- Group appointment created with capacity limit
- Multiple patients addable to single group appointment
- Participant list maintained
- Attendance trackable for each participant
- Group appointment displays participant count on calendar
- Individual participant removable without deleting group
- Group encounter creatable for all participants
- Group billing supported

### FR-AS-006: Appointment Reminders
**Description:** The system shall send automated appointment reminders to patients via email and SMS.

**Acceptance Criteria:**
- Reminder timing configurable (24 hours, 48 hours, 2 hours before appointment)
- Email reminders sent to patient email address
- SMS reminders sent to patient mobile phone
- Reminder content includes: date, time, provider, facility, instructions
- Patient confirmation link included in reminder
- Confirmation status updated when patient confirms
- Cancellation request link included
- Opt-out mechanism for patients not wanting reminders
- Reminder send status logged

### FR-AS-007: Find Next Available Appointment
**Description:** The system shall quickly identify next available appointment slot for specified provider and criteria.

**Acceptance Criteria:**
- Search parameters: provider (required), facility, appointment type, duration, date range
- Algorithm scans calendar for open slots
- Returns first available slot OR multiple options
- Considers provider schedule/availability
- Respects facility operating hours
- Excludes blocked time and existing appointments
- Search completes within 3 seconds
- Selected slot pre-fills new appointment form

### FR-AS-008: Patient Flow Board
**Description:** The system shall provide real-time dashboard showing current patients in office with status tracking.

**Acceptance Criteria:**
- Displays today's appointments with current status
- Status progression: Not Arrived → Arrived → In Room → In Progress → Checkout → Completed
- Check-in function updates status to Arrived
- Room assignment displayable
- Wait time calculated and displayed (arrival to room, room to provider)
- Color coding for wait time thresholds (green/yellow/red)
- Real-time updates via AJAX polling (30-60 second refresh)
- Filter by provider, facility, time range
- Provider queue shows only their patients

### FR-AS-009: Appointment Search and Reporting
**Description:** The system shall provide search and reporting functionality for appointment data.

**Acceptance Criteria:**
- Search by patient, provider, facility, date range, status, category
- Export results to CSV/Excel
- Reports include: appointments by provider, no-show report, confirmation report, appointment volume
- Date range selector (custom dates, last week, last month, etc.)
- Results paginated for large datasets
- Drill-down to appointment details from reports
- Schedule reports for automatic generation and email

### FR-AS-010: Appointment Conflict Detection
**Description:** The system shall detect and warn users about scheduling conflicts and double-bookings.

**Acceptance Criteria:**
- System checks for overlapping appointments for same provider
- Warning displayed when double-booking detected (if prevention not enforced)
- System allows override with permission for intentional double-booking
- Patient double-booking detected (same patient, overlapping times, different providers)
- Resource conflicts detected (room, equipment)
- Conflict checking configurable (warning vs prevention)

---

## Non-Functional Requirements

### NFR-AS-001: Performance - Calendar Loading
**Description:** Calendar views shall load quickly to support efficient scheduling workflow.

**Acceptance Criteria:**
- Day view loads within 1 second
- Week view loads within 2 seconds
- Month view loads within 3 seconds
- Multi-provider view (5 providers) loads within 3 seconds
- Appointment creation saves within 1 second
- Calendar supports 1000+ appointments per provider per year
- Concurrent users (50+) without performance degradation

### NFR-AS-002: Usability - Scheduling Efficiency
**Description:** Appointment scheduling interface shall support rapid, error-free scheduling.

**Acceptance Criteria:**
- Appointment creatable in under 30 seconds for experienced user
- Patient search auto-complete with type-ahead
- Provider selection defaults intelligently
- Duration defaults based on appointment category
- Facility auto-populated based on provider
- Keyboard shortcuts for common actions (Ctrl+N for new appointment)
- Visual feedback for all actions (confirmation messages)
- Undo functionality for accidental deletions

### NFR-AS-003: Availability - Business Hours
**Description:** Scheduling system shall be available during all clinic operating hours.

**Acceptance Criteria:**
- System uptime 99.5% during business hours (6am-10pm local time)
- Scheduled maintenance only during off-hours (10pm-6am)
- Maximum unplanned downtime 1 hour per month
- Graceful degradation if reminder service unavailable
- Local caching prevents data loss during brief network interruptions

### NFR-AS-004: Scalability - Appointment Volume
**Description:** System shall scale to handle large appointment volumes across multiple providers and facilities.

**Acceptance Criteria:**
- Supports minimum 50 concurrent providers
- Supports minimum 10 facilities
- Handles 1000+ appointments per day
- Calendar performance maintained with 100,000+ total appointments
- Database partitioning supported for very large datasets
- Archive capability for historical appointments (>2 years old)

### NFR-AS-005: Reliability - Data Integrity
**Description:** Appointment data shall be protected against loss or corruption.

**Acceptance Criteria:**
- Database transactions ensure atomic operations
- Referential integrity maintained (appointments linked to valid patients/providers)
- Auto-save prevents data loss during entry
- Backup and recovery tested quarterly
- Concurrent edit detection prevents data overwrites
- Appointment deletion requires confirmation

### NFR-AS-006: Security - Access Control
**Description:** Appointment scheduling shall enforce role-based access controls.

**Acceptance Criteria:**
- ACL enforced for calendar view, create, edit, delete permissions
- Users see only authorized providers' calendars (facility restrictions)
- Appointment access logged with user, timestamp, IP
- Sensitive appointment types (mental health, HIV, etc.) have enhanced privacy
- PHI in reminders limited (no diagnosis/reason in SMS)
- TLS 1.2+ required for transmission

### NFR-AS-007: Compliance - HIPAA
**Description:** Appointment scheduling shall comply with HIPAA requirements.

**Acceptance Criteria:**
- Minimum necessary access enforced
- Audit trail for all appointment access
- Patient consent for automated reminders documented
- Breach notification process for unauthorized access
- Business Associate Agreements with reminder vendors
- Appointment reminders HIPAA-compliant (minimal PHI)

### NFR-AS-008: Integration - Calendar Systems
**Description:** Appointment data shall integrate with external calendar systems.

**Acceptance Criteria:**
- iCalendar (ICS) export supported
- Provider calendar sync to Outlook/Google Calendar (optional)
- HL7 SIU messages for appointment scheduling (if configured)
- FHIR Appointment resource accessible via API
- Real-time updates propagated to integrated systems within 5 minutes

### NFR-AS-009: Localization - Multi-Language
**Description:** Scheduling interface shall support multiple languages.

**Acceptance Criteria:**
- Calendar interface translatable to all system-supported languages
- Date/time formats localized per user preference
- Appointment categories translatable
- Reminder messages sent in patient's preferred language
- Right-to-left language support (Arabic, Hebrew)

### NFR-AS-010: Maintainability - Configuration
**Description:** Scheduling system shall be configurable without code changes.

**Acceptance Criteria:**
- Appointment categories configurable via admin interface
- Color coding customizable
- Time slot intervals configurable (5, 10, 15, 30 minute slots)
- Facility hours configurable
- Provider schedules/availability configurable
- Reminder timing and content templates configurable
- Configuration changes effective immediately (no restart required)

---

## Data Quality Requirements

### DQ-AS-001: Appointment Completeness
**Description:** Appointment records shall have complete essential information.

**Acceptance Criteria:**
- 100% of appointments have patient, provider, date, time, duration
- 95% of appointments have facility assigned
- 90% of appointments have category/type assigned
- 85% of appointments have reason/chief complaint documented
- Missing data highlighted in calendar view

### DQ-AS-002: Appointment Accuracy
**Description:** Appointment data shall be accurate and current.

**Acceptance Criteria:**
- Patient and provider references validated on save
- Facility exists and active
- Date/time valid (not in distant past or unreasonable future)
- Duration within acceptable range (5 min to 8 hours)
- Status transitions logical (cannot mark completed before arrived)
- Duplicate appointment detection (same patient, same day, same provider)

### DQ-AS-003: Reminder Deliverability
**Description:** Appointment reminders shall successfully reach patients.

**Acceptance Criteria:**
- Email address format validated before reminder send
- Mobile phone number validated before SMS send
- Bounce/failure rate <5%
- Failed delivery logged and flagged for manual follow-up
- Contact information updated when delivery fails
- Delivery confirmation rate >90% for sent reminders

---

## Integration Requirements

### IR-AS-001: FHIR API
**Description:** Appointments shall be accessible and manageable via FHIR R4 API.

**Acceptance Criteria:**
- GET /fhir/Appointment returns appointment resources
- POST /fhir/Appointment creates new appointments
- PUT /fhir/Appointment updates appointments
- DELETE /fhir/Appointment cancels appointments
- Appointment resource conforms to US Core profile
- Search parameters: patient, practitioner, date, status
- OAuth2 authentication enforced

### IR-AS-002: Patient Portal Integration
**Description:** Patients shall request appointments through patient portal.

**Acceptance Criteria:**
- Portal displays appointment request form
- Patient selects provider, preferred dates/times
- Request creates pending appointment or notification to scheduler
- Patient receives confirmation when appointment scheduled
- Patients view upcoming appointments in portal
- Patients cancel/reschedule appointments via portal (if enabled)
- Portal changes sync to scheduling system immediately

### IR-AS-003: Encounter Integration
**Description:** Appointments shall integrate with encounter workflow.

**Acceptance Criteria:**
- Completed appointments optionally auto-create encounters
- Encounter linked to appointment via reference
- Appointment reason pre-populates encounter chief complaint
- Encounter provider defaults from appointment provider
- Encounter facility defaults from appointment facility
- Deleting appointment warns if encounter exists

### IR-AS-004: Billing Integration
**Description:** Appointment data shall support billing workflow.

**Acceptance Criteria:**
- Appointment type maps to default CPT codes for billing
- Insurance information accessible from appointment
- No-show appointments billable with appropriate CPT code
- Appointment data included in billing reports
- Cancelled appointment tracking for revenue loss analysis

---

## Reminder-Specific Requirements

### RR-AS-001: Email Reminders
**Description:** System shall send appointment reminders via email.

**Acceptance Criteria:**
- Email template includes: patient name, appointment date/time, provider name, facility name/address, contact phone
- Confirmation link included (optional)
- Cancellation/reschedule request link included (optional)
- Email sent from configured sender address
- Email delivery status tracked (sent, delivered, bounced, failed)
- Failed deliveries logged and flagged
- Unsubscribe mechanism included

### RR-AS-002: SMS Reminders
**Description:** System shall send appointment reminders via SMS text message.

**Acceptance Criteria:**
- SMS message concise (<160 characters preferred)
- Message includes: date, time, provider, facility, phone number
- Confirmation via reply supported ("Reply Y to confirm")
- SMS sent via configured gateway (Twilio, Clickatell, etc.)
- Delivery status tracked
- Opt-out mechanism supported ("Reply STOP to opt out")
- HIPAA-compliant (minimal PHI in message)

### RR-AS-003: Automated Phone Reminders
**Description:** System shall support automated phone call reminders.

**Acceptance Criteria:**
- Automated phone system dials patient phone number
- Pre-recorded or text-to-speech message plays
- Patient confirms via keypress (Press 1 to confirm)
- Confirmation status updated in system
- Failed call attempts logged
- Multiple attempts for unanswered calls (configurable)

---

## Total Requirements: 37
- Functional Requirements: 10
- Non-Functional Requirements: 10
- Data Quality Requirements: 3
- Integration Requirements: 4
- Reminder-Specific Requirements: 3
- Additional scheduling requirements: 7

---

**End of Appointment Scheduling Requirements**
