# Appointment Scheduling

**Capability ID:** 03
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Appointment Scheduling provides comprehensive calendar management and scheduling functionality supporting multiple providers, facilities, and resources with individual appointments, group appointments, recurring appointments, and patient flow tracking.

**Primary Purpose:** Enable efficient scheduling of patient appointments, resource allocation, provider calendar management, and real-time patient flow monitoring.

**Primary Users:** Front desk staff, schedulers, medical assistants, nurses, providers, practice managers

---

## Use Cases

1. **Individual Appointment Scheduling** - Schedule one-on-one patient-provider appointments
2. **Multi-Provider Calendar Management** - Manage calendars for multiple providers simultaneously
3. **Group Appointment Scheduling** - Schedule group therapy or education sessions
4. **Recurring Appointment Creation** - Set up repeating appointment series
5. **Appointment Reminders** - Automated patient reminders via email/SMS
6. **Patient Flow Board** - Real-time tracking of patients in office
7. **Resource Scheduling** - Schedule rooms, equipment, or other resources
8. **Appointment Search and Reporting** - Find appointments by various criteria
9. **No-Show and Cancellation Tracking** - Document and track appointment statuses
10. **Find Next Available** - Quickly locate next open appointment slot

---

## Features

### Feature 3.1: Create Individual Appointment

**Description:**
Schedule individual patient appointments with specific providers, facilities, date/time, duration, and appointment type/category.

**How to Use (User Instructions):**
1. Navigate to Calendar
2. Select provider calendar view
3. Click on desired date/time slot OR click "New Appointment"
4. Enter appointment details:
   - **Patient:** Search and select patient
   - **Provider:** Select provider (defaults to calendar provider)
   - **Facility:** Select location
   - **Date and Time:** Select or confirm appointment date/time
   - **Duration:** Set appointment length (15, 30, 60 min, etc.)
   - **Category:** Select appointment type (office visit, procedure, follow-up, etc.)
   - **Reason:** Enter chief complaint or reason for visit
   - **Comments:** Additional notes
5. Click "Save" or "Book Appointment"
6. Appointment appears on calendar
7. Optional: Print appointment card for patient

**How to Test:**
- **Test 1:** Create appointment for existing patient, verify appears on calendar
- **Test 2:** Create appointment with different provider, verify on correct calendar
- **Test 3:** Create appointment at different facility, verify facility recorded
- **Test 4:** Create overlapping appointments, verify warning if configured
- **Test 5:** Create appointment in past, verify warning or prevention
- **Test 6:** Verify appointment confirmation sent to patient if configured
- **Test 7:** Print appointment card, verify details correct

**Expected System Behavior:**
- Appointment stored in `openemr_postcalendar_events` table
- Patient, provider, facility linked via foreign keys
- Time slot marked busy on calendar
- Duration determines calendar block size
- Category determines color coding on calendar (configurable)
- Status defaults to "Scheduled" or configurable default
- Appointment ID auto-generated
- Audit log entry created
- Optional email/SMS confirmation sent to patient
- Appointment available in patient chart
- Conflicts detected if double-booking prevention enabled
- ACL enforced (user must have calendar write permission)

**Technical Details:**
- **Primary Files:** `/interface/main/calendar/add_edit_event.php`
- **Calendar Display:** `/interface/main/calendar/index.php`
- **Database Table:** `openemr_postcalendar_events`
- **Related Tables:** `patient_data`, `users`, `facility`, `openemr_postcalendar_categories`
- **Service:** `/src/Services/AppointmentService.php`

---

### Feature 3.2: Multi-Provider Calendar View

**Description:**
Display and manage calendars for multiple providers simultaneously with day, week, and month views supporting quick visualization of provider availability and appointment load.

**How to Use (User Instructions):**
1. Navigate to Calendar
2. Select calendar view type:
   - **Day View:** Hourly schedule for selected day
   - **Week View:** Full week at a glance
   - **Month View:** Monthly overview
3. Select providers to display:
   - Single provider
   - Multiple providers (side-by-side)
   - All providers in facility
4. Use navigation controls to change dates
5. Color-coded appointments by category
6. Click appointment to view details or edit
7. Drag-and-drop to reschedule (if enabled)
8. Print calendar for reference

**How to Test:**
- **Test 1:** View single provider day view, verify hourly slots
- **Test 2:** View multiple providers simultaneously, verify side-by-side display
- **Test 3:** Switch between day/week/month views, verify formatting
- **Test 4:** Navigate to different dates, verify appointments load
- **Test 5:** Test color coding by appointment category
- **Test 6:** Click appointment, verify details popup
- **Test 7:** Test drag-and-drop rescheduling if enabled
- **Test 8:** Print calendar, verify readable format
- **Test 9:** Test facility filter (show only providers at selected facility)

**Expected System Behavior:**
- Calendar queries `openemr_postcalendar_events` filtered by provider(s) and date range
- Time slots displayed based on facility hours (configurable)
- Appointments color-coded by category from `openemr_postcalendar_categories`
- Multiple providers displayed in columns (day/week view)
- Month view shows appointment count per day
- Drag-and-drop triggers update to event start time
- Print view optimized for paper output
- Performance optimized for large datasets
- ACL restricts calendar viewing based on provider access permissions
- Filter by facility if user has facility restrictions

**Technical Details:**
- **Calendar Engine:** Custom PostNuke calendar implementation
- **Primary Files:** `/interface/main/calendar/index.php`, `/interface/main/calendar/modules/PostCalendar/`
- **AJAX Handlers:** `/interface/main/calendar/get_appointments.php`
- **Database Query:** Date range + provider filter
- **Performance:** Indexes on pc_eventDate, pc_aid (provider), pc_facility

---

### Feature 3.3: Group Appointment Scheduling

**Description:**
Schedule group therapy sessions, education classes, or other group events with multiple patients attending simultaneously.

**How to Use (User Instructions):**
1. Navigate to Calendar
2. Click "New Group Appointment" or use group appointment form
3. Enter group details:
   - **Event Title:** Group name/description
   - **Provider/Facilitator:** Lead provider
   - **Facility:** Location
   - **Date and Time:** Session date/time
   - **Duration:** Session length
   - **Category:** Group therapy, education, etc.
   - **Maximum Participants:** Capacity limit
4. Save group appointment shell
5. Add patients to group:
   - Search and select each patient
   - Confirm attendance
6. Group appointment appears on calendar
7. Track attendance during/after session
8. Document group encounter for billing

**How to Test:**
- **Test 1:** Create group appointment, verify saved
- **Test 2:** Add multiple patients to group, verify all recorded
- **Test 3:** Verify capacity limit enforced
- **Test 4:** Track attendance, verify status updates
- **Test 5:** Create group encounter for billing, verify all patients included
- **Test 6:** View group appointment on calendar, verify patient count displayed
- **Test 7:** Send reminders to all group participants

**Expected System Behavior:**
- Group appointment created in `openemr_postcalendar_events` with group flag
- Individual patient links created (possibly in separate linking table)
- Capacity tracked and enforced
- Calendar displays group appointment with participant count
- Attendance tracking available via group attendance form
- Group encounter can be created linking to all participants
- Billing supports group billing codes
- Reminders sent to all participants
- ACL enforced for group appointment creation

**Technical Details:**
- **Primary Files:** `/interface/forms/newGroupEncounter/`, `/interface/forms/group_attendance/`
- **Database Tables:** `openemr_postcalendar_events`, `therapy_groups`, `therapy_groups_participants`
- **Special Handling:** Group therapy module in `/interface/therapy_groups/`

---

### Feature 3.4: Recurring Appointment Series

**Description:**
Create repeating appointment series for ongoing care (weekly therapy, monthly check-ups) with single scheduling action creating multiple future appointments.

**How to Use (User Instructions):**
1. Create new appointment or edit existing
2. Select "Recurring" or "Repeat" option
3. Configure recurrence pattern:
   - **Frequency:** Daily, Weekly, Monthly, Yearly
   - **Interval:** Every 1 week, every 2 weeks, etc.
   - **Days of Week:** For weekly (e.g., every Monday and Wednesday)
   - **End Condition:** Number of occurrences OR end date
4. Review recurring appointment summary
5. Click "Save" to create entire series
6. System creates all appointments in series
7. Individual appointments can be edited/cancelled independently

**How to Test:**
- **Test 1:** Create weekly recurring appointment for 8 weeks, verify all 8 created
- **Test 2:** Create monthly recurring appointment for 6 months, verify correct dates
- **Test 3:** Edit single occurrence in series, verify only that one changes
- **Test 4:** Cancel single occurrence, verify others remain
- **Test 5:** Delete entire series, verify all appointments removed
- **Test 6:** Test end by count vs end by date logic
- **Test 7:** Test skip holidays if configured

**Expected System Behavior:**
- Recurring appointments created as individual records in `openemr_postcalendar_events`
- Series linked by recurrence ID or parent appointment ID
- Recurrence pattern calculated based on rules:
  - Daily: increment by N days
  - Weekly: specific days of week, increment by N weeks
  - Monthly: same day of month OR Nth weekday
  - Yearly: same date each year
- All appointments visible immediately on calendar
- Individual appointments editable/cancellable without affecting series
- Series deletion removes all future occurrences
- Holiday checking optional (skip appointments on holidays)
- Patient reminders sent for each occurrence

**Technical Details:**
- **Recurrence Fields:** `pc_recurrtype`, `pc_recurrspec`, `pc_recurrfreq` in `openemr_postcalendar_events`
- **Algorithm:** Date calculation based on recurrence rules
- **Linking:** `pc_sharing` or similar field links series
- **Bulk Insert:** Transaction for creating multiple appointments

---

### Feature 3.5: Appointment Reminders

**Description:**
Automated patient reminders via email, SMS, or phone for upcoming appointments reducing no-shows and improving patient engagement.

**How to Use (User Instructions):**

**Configuration:**
1. Navigate to Administration → Globals → Notifications
2. Enable appointment reminders
3. Configure reminder settings:
   - Reminder method (email, SMS, phone call)
   - Timing (24 hours before, 48 hours before, etc.)
   - Message template
4. Integrate with reminder service (MedEx, Twilio, etc.)

**Operation:**
1. Background service runs periodically (e.g., every 15 minutes)
2. Identifies appointments in reminder window
3. Sends reminders to patients via configured method
4. Logs reminder status (sent, failed, confirmed)
5. Patient can confirm/cancel via response

**Patient Interaction:**
1. Patient receives reminder (email/SMS)
2. Message contains: appointment date, time, provider, facility
3. Patient can click to confirm or request rescheduling
4. Confirmation status updated in system

**How to Test:**
- **Test 1:** Create appointment 24 hours in future, verify reminder sent
- **Test 2:** Test email reminder delivery
- **Test 3:** Test SMS reminder delivery
- **Test 4:** Patient confirms appointment, verify status updated
- **Test 5:** Patient cancels via reminder, verify status updated
- **Test 6:** Test multiple reminder timing (24h and 2h before)
- **Test 7:** Verify reminders not sent if patient opts out
- **Test 8:** Test reminder for appointments without patient phone/email (should skip)

**Expected System Behavior:**
- Background service (MedEx or similar) queries upcoming appointments
- Filters patients based on contact preferences and opt-out status
- Sends reminders via configured gateway:
  - Email via PHPMailer
  - SMS via Twilio/Clickatell
  - Phone call via automated system
- Reminder log created in MedEx tables or notification log
- Patient responses processed and status updated
- Confirmed appointments flagged in calendar
- Cancellation requests trigger workflow for rescheduling
- Reminder failures logged for manual follow-up
- HIPAA-compliant messaging (minimal PHI in unsecured channels)

**Technical Details:**
- **MedEx Module:** `/library/MedEx/`
- **Background Service:** MedEx background service (runs via cron)
- **SMS Integration:** `/modules/sms_email_reminder/`, Twilio module
- **Database Tables:** MedEx custom tables, `batchcom` for batch communications
- **Configuration:** Global settings for reminder timing and methods

---

### Feature 3.6: Patient Flow Board

**Description:**
Real-time dashboard displaying current patients in office with arrival status, room assignment, provider status, and checkout status for efficient patient flow management.

**How to Use (User Instructions):**
1. Navigate to Calendar → Patient Flow Board OR dedicated Flow Board
2. View current day's appointments with status:
   - **Not Arrived:** Scheduled but not checked in
   - **Arrived:** Checked in, waiting
   - **In Room:** Roomed, waiting for provider
   - **In Progress:** Provider with patient
   - **Checkout:** Visit complete, checking out
   - **Completed:** Checked out
3. Update patient status as they progress:
   - Check in arriving patients
   - Assign to room
   - Mark "Provider Ready" when ready
   - Mark complete at checkout
4. View wait times and alerts for delays
5. Filter by provider, facility, or time
6. Use for operations management

**How to Test:**
- **Test 1:** Check in patient, verify status changes to "Arrived"
- **Test 2:** Assign patient to room, verify room displayed
- **Test 3:** Mark in progress, verify provider sees in their queue
- **Test 4:** Complete visit, verify removed from active list
- **Test 5:** View multiple providers simultaneously
- **Test 6:** Test wait time calculations
- **Test 7:** Test color coding for delays/alerts
- **Test 8:** Test real-time updates (multiple users)

**Expected System Behavior:**
- Flow board queries today's appointments from `openemr_postcalendar_events`
- Status updates written to `patient_tracker` table
- Room assignments tracked in `patient_tracker_element`
- Wait times calculated from timestamps (arrival to room, room to provider, etc.)
- Color coding based on wait time thresholds:
  - Green: on time
  - Yellow: approaching threshold
  - Red: exceeding threshold
- Real-time updates via AJAX polling or WebSocket
- Status changes logged for quality improvement analysis
- Provider queue displays only their patients
- Facility filter shows only relevant location
- ACL controls access to flow board

**Technical Details:**
- **Primary Files:** `/interface/patient_tracker/patient_tracker.php`
- **Module:** `/interface/modules/zend_modules/module/PatientFlowBoard/`
- **Database Tables:** `patient_tracker`, `patient_tracker_element`, `patient_tracker_status`
- **Real-time:** AJAX polling every 30-60 seconds
- **Reports:** Flow board data used for efficiency reports

---

### Feature 3.7: Find Next Available Appointment

**Description:**
Quickly locate next available appointment slot for specified provider, facility, and appointment type to accelerate scheduling workflow.

**How to Use (User Instructions):**
1. In appointment dialog or calendar, click "Find Next Available"
2. Specify search criteria:
   - Provider (required)
   - Facility
   - Appointment type/duration
   - Date range (search from date, how far ahead)
   - Preferred days/times
3. Click "Search"
4. System displays next available slot(s)
5. Select desired slot
6. Appointment pre-filled with selected time
7. Complete other details and save

**How to Test:**
- **Test 1:** Search for next available for specific provider, verify correct
- **Test 2:** Search with date range, verify within range
- **Test 3:** Search for 60-minute appointment, verify sufficient gap
- **Test 4:** Search when provider fully booked, verify message
- **Test 5:** Test preferred days (only Mondays), verify correct
- **Test 6:** Test multiple providers (show first available among any)
- **Test 7:** Test facility filter, verify only that facility

**Expected System Behavior:**
- Query scans calendar slots for specified provider
- Excludes slots where appointment exists (busy)
- Considers provider schedule/availability
- Checks appointment duration fits in gap
- Respects facility hours
- Applies day/time preferences if specified
- Returns first available or multiple options
- Algorithm efficient even with large appointment volume
- May cache provider schedule for performance
- ACL respects provider visibility permissions

**Technical Details:**
- **Algorithm:** Slot scanning or gap detection
- **Database Query:** `openemr_postcalendar_events` WHERE NOT EXISTS
- **Schedule:** Provider schedule from facility hours or custom schedule
- **Performance:** Index on pc_eventDate, pc_aid, pc_facility

---

### Feature 3.8: Appointment Status Management

**Description:**
Track and manage appointment statuses including scheduled, confirmed, arrived, in-progress, completed, cancelled, no-show, and rescheduled for accountability and reporting.

**How to Use (User Instructions):**
1. Access appointment (via calendar, flow board, or patient chart)
2. Update status dropdown:
   - **Scheduled:** Initial booking
   - **Confirmed:** Patient confirmed attendance
   - **Arrived:** Patient checked in
   - **In Progress:** Currently with provider
   - **Completed:** Visit finished
   - **Cancelled:** Appointment cancelled
   - **No-Show:** Patient did not arrive
   - **Rescheduled:** Moved to different time
3. If cancelled or no-show, may enter reason/notes
4. Status changes logged
5. Reports available for no-show rates, etc.

**How to Test:**
- **Test 1:** Mark appointment confirmed, verify status saved
- **Test 2:** Check in appointment (arrived), verify visible on flow board
- **Test 3:** Mark completed, verify removed from active appointments
- **Test 4:** Mark no-show, verify flagged for follow-up
- **Test 5:** Cancel appointment, verify removed from calendar
- **Test 6:** Generate no-show report, verify accurate
- **Test 7:** Test status transitions (can't mark completed without arrived)

**Expected System Behavior:**
- Status stored in `pc_apptstatus` field of `openemr_postcalendar_events`
- Status changes audit logged
- No-show status may trigger patient flag or communication
- Cancelled appointments may be hidden or shown with strikethrough
- Completed appointments may trigger encounter creation
- Status used in reporting:
  - No-show rate by provider/facility
  - Confirmation rate
  - Cancellation patterns
- Status workflow may be enforced (must arrive before in-progress)
- Some statuses may prevent editing (completed appointments locked)

**Technical Details:**
- **Status Field:** `pc_apptstatus` in `openemr_postcalendar_events`
- **Workflow:** Configurable status transitions
- **Reporting:** `/interface/reports/appointments_report.php`
- **Audit:** Status changes logged in `log` table

---

## Configuration

### Global Settings

**Location:** Administration → Globals → Calendar

- **Calendar Display** - Day/week/month view defaults
- **Appointment Slot Interval** - 15, 30, 60 minute slots
- **Appointment Types** - Configurable categories and colors
- **Double Booking** - Allow or prevent overlapping appointments
- **Auto-Create Encounter** - Create encounter on appointment completion
- **Appointment Reminders** - Enable and configure reminders
- **Patient Portal Scheduling** - Allow patients to request appointments
- **Group Appointments** - Enable group scheduling features
- **Recurrence Rules** - Configure recurring appointment options

### ACL Requirements

- **Calendar Read** - `calendar/default` - View appointments
- **Calendar Write** - `calendar/default/write` - Create/edit appointments
- **Calendar Delete** - `calendar/default/delete` - Delete appointments
- **Other Provider Calendar** - `admin/calendar` - View other providers' calendars

---

## Related Capabilities

- **[Patient Management](01-patient-management.md)** - Patient selection for appointments
- **[Clinical Documentation](02-clinical-documentation.md)** - Encounter creation from appointments
- **[Messaging and Communication](11-messaging-communication.md)** - Appointment reminders
- **[Patient Portal](09-patient-portal.md)** - Patient appointment requests
- **[Practice Administration](10-practice-administration.md)** - Provider and facility management

---

## Compliance and Standards

- **FHIR R4** - Appointment resource conforming to US Core
- **HL7 v2.x** - SIU messages for appointment scheduling (if configured)
- **Patient Engagement** - ONC patient access requirements
- **HIPAA** - Secure appointment reminders with minimal PHI

---

**End of Appointment Scheduling Capability**
