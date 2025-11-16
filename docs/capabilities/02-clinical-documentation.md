# Clinical Documentation

**Capability ID:** 02
**Version:** 7.0.4+
**Last Updated:** 2025-11-15

---

## Overview

Clinical Documentation provides comprehensive tools for recording patient encounters, clinical assessments, procedures, observations, and care plans. The system supports 37+ built-in form types plus unlimited custom forms through the Layout-Based Form (LBF) builder.

**Primary Purpose:** Enable providers and clinical staff to document patient care in structured, searchable, interoperable formats that support clinical decision-making, billing, quality measurement, and regulatory compliance.

**Primary Users:** Physicians, nurse practitioners, physician assistants, nurses, medical assistants, behavioral health specialists, therapists

---

## Use Cases

1. **Encounter Documentation** - Document patient visits with SOAP notes, clinical assessments, and procedures
2. **Problem List Management** - Maintain active and historical problem/diagnosis lists
3. **Medication List Management** - Track current and past medications
4. **Allergy Documentation** - Record and maintain patient allergies and adverse reactions
5. **Immunization Tracking** - Document vaccinations and maintain immunization history
6. **Vital Signs Recording** - Capture and trend vital signs
7. **Specialty Documentation** - Use specialty-specific forms (ophthalmology, behavioral health, etc.)
8. **Assessment Tools** - Administer standardized assessments (PHQ-9, GAD-7, SDOH)
9. **Care Planning** - Create and maintain patient care plans
10. **Custom Form Creation** - Build custom forms for specialty-specific workflows

---

## Features

### Feature 2.1: Encounter Creation and Management

**Description:**
Create and manage patient encounters representing individual visits, documenting encounter metadata including date, time, facility, provider, reason for visit, and encounter type.

**How to Use (User Instructions):**
1. Open patient chart
2. Navigate to Encounter or click "New Encounter"
3. Fill in encounter details:
   - Encounter date and time
   - Facility/location
   - Provider (rendering and supervising)
   - Reason for visit/chief complaint
   - Encounter category (office visit, hospital, nursing facility, etc.)
   - Billing facility and POS code
4. Click "Create" to establish encounter
5. Add clinical forms and documentation to encounter
6. Close encounter when documentation complete

**How to Test:**
- **Test 1:** Create new office visit encounter, verify saved correctly
- **Test 2:** Create encounter for different facility, verify facility-specific settings applied
- **Test 3:** Create encounter with supervising provider, verify both providers recorded
- **Test 4:** Back-date encounter, verify date validation allows past dates
- **Test 5:** Verify encounter appears in encounter list
- **Test 6:** Lock encounter, verify cannot be edited after lock
- **Test 7:** Verify audit log records encounter creation

**Expected System Behavior:**
- Encounter stored in `form_encounter` table
- Unique encounter number assigned (auto-increment or configured format)
- Encounter linked to patient via PID
- Facility determines default billing settings and POS code
- Provider determines billing provider on claims
- Encounter serves as container for all forms/documents created during visit
- Encounter status tracked (pending, closed, billed, locked)
- Encounter date/time determines chronological order in chart
- ACL enforced (user must have encounter create permission)
- Audit log entry created

**Technical Details:**
- **Primary Files:** `/interface/forms/newpatient/new.php`, `/interface/patient_file/encounter/encounter_top.php`
- **Service:** `/src/Services/EncounterService.php`
- **Database Table:** `form_encounter`
- **Related Tables:** `facility` (location), `users` (provider), `patient_data` (patient)

---

### Feature 2.2: SOAP Notes

**Description:**
Structured clinical documentation using SOAP (Subjective, Objective, Assessment, Plan) format for recording clinical encounters in a standardized manner.

**How to Use (User Instructions):**
1. Within an encounter, click "Add Form"
2. Select "SOAP" from form list
3. Document each section:
   - **Subjective:** Patient's description of symptoms, concerns, history
   - **Objective:** Clinical findings, vital signs, exam results, test results
   - **Assessment:** Clinical assessment, differential diagnosis, working diagnosis
   - **Plan:** Treatment plan, orders, follow-up, patient instructions
4. Click "Save" to store SOAP note
5. Note appears in encounter and is searchable
6. Can print or include in clinical summaries

**How to Test:**
- **Test 1:** Create SOAP note with all four sections populated
- **Test 2:** Create SOAP note with only subjective and assessment (partial documentation)
- **Test 3:** Edit existing SOAP note, verify changes saved and audit logged
- **Test 4:** Print SOAP note, verify formatting
- **Test 5:** Search for text within SOAP notes, verify full-text search works
- **Test 6:** Include SOAP in CCDA export, verify narrative sections populated
- **Test 7:** Verify ACL prevents unauthorized editing

**Expected System Behavior:**
- SOAP note stored in `form_soap` table
- Each section stored in separate text field
- Form linked to encounter via encounter ID
- Form appears in encounter's form list
- Text searchable through database full-text search
- Print functionality generates formatted output
- ACL enforced for create/edit/view
- Amendment tracking if note edited after encounter close
- CCDA export includes SOAP sections in appropriate narrative sections
- Audit log entry for create/edit

**Technical Details:**
- **Location:** `/interface/forms/soap/`
- **Files:** `new.php` (create), `view.php` (display), `save.php` (save handler)
- **Database Table:** `form_soap`
- **Fields:** subjective (text), objective (text), assessment (text), plan (text)

---

### Feature 2.3: Vital Signs Recording

**Description:**
Record and track patient vital signs including blood pressure, pulse, respiration, temperature, height, weight, BMI, oxygen saturation, and pain scale.

**How to Use (User Instructions):**
1. Within encounter, add "Vitals" form
2. Record vital signs:
   - Blood Pressure (systolic/diastolic, position, location)
   - Pulse (rate, rhythm, location)
   - Respiration (rate, rhythm)
   - Temperature (value, method/route)
   - Height and Weight
   - Oxygen Saturation (SpO2, oxygen flow rate if applicable)
   - Pain Scale (0-10)
   - Head Circumference (pediatric)
   - BMI (auto-calculated from height/weight)
3. Select qualifiers (standing, sitting, lying for BP)
4. Click "Save"
5. Vitals display in flowsheet for trending

**How to Test:**
- **Test 1:** Record complete vital signs, verify all values saved
- **Test 2:** Verify BMI auto-calculation from height and weight
- **Test 3:** Test vital signs flowsheet display (trending over time)
- **Test 4:** Test vital sign alerts/flags for abnormal values if configured
- **Test 5:** Export to FHIR, verify Observation resources created for vitals
- **Test 6:** Include in CCDA, verify vitals in Vital Signs section
- **Test 7:** Test temperature conversion (Fahrenheit/Celsius)
- **Test 8:** Test imperial/metric unit conversion for height/weight

**Expected System Behavior:**
- Vitals stored in `form_vitals` table
- Each vital sign stored in dedicated field with units
- BMI auto-calculated using formula: weight(kg) / (height(m)²)
- Values validated for reasonable ranges (warnings for extreme values)
- Historical vital signs displayed in flowsheet/graph
- FHIR export creates Observation resources with appropriate LOINC codes:
  - Blood Pressure: 85354-9 (panel), 8480-6 (systolic), 8462-4 (diastolic)
  - Pulse: 8867-4
  - Respiration: 9279-1
  - Temperature: 8310-5
  - Weight: 29463-7
  - Height: 8302-2
  - BMI: 39156-5
  - Oxygen Saturation: 2708-6
- CCDA export includes vitals in Vital Signs section
- Alerts can trigger clinical decision rules

**Technical Details:**
- **Location:** `/interface/forms/vitals/`
- **Database Table:** `form_vitals`
- **FHIR Controller:** `/src/RestControllers/FHIR/FhirObservationRestController.php`
- **LOINC Codes:** Standardized observation codes for interoperability

---

### Feature 2.4: Problem List Management

**Description:**
Maintain active and historical patient problem/diagnosis list with ICD-10 codes, SNOMED codes, onset dates, resolution dates, and clinical status.

**How to Use (User Instructions):**
1. Open patient chart
2. Navigate to Medical Problems section
3. Click "Add Problem"
4. Search for condition by:
   - Description (text search)
   - ICD-10 code
   - SNOMED code
5. Select matching diagnosis
6. Enter additional information:
   - Onset date
   - Clinical status (active, resolved, inactive)
   - Severity (mild, moderate, severe)
   - Verification status (confirmed, provisional)
   - Referral source
7. Click "Save"
8. Problem appears in active problem list
9. To resolve: Edit problem, change status to "Resolved," enter resolution date

**How to Test:**
- **Test 1:** Add active problem with ICD-10 code, verify appears in problem list
- **Test 2:** Add problem with SNOMED code, verify code stored
- **Test 3:** Resolve existing problem, verify moves to historical list
- **Test 4:** Reactivate resolved problem, verify returns to active list
- **Test 5:** Export to FHIR, verify Condition resource created
- **Test 6:** Include in CCDA, verify appears in Problem List section
- **Test 7:** Test problem list in encounter billing (problems available for coding)
- **Test 8:** Test search functionality for diagnoses

**Expected System Behavior:**
- Problems stored in `lists` table where type='medical_problem'
- ICD-10 code stored in diagnosis field
- SNOMED code stored in external reference field
- Clinical status determines active vs resolved categorization
- Problems available for encounter billing (diagnosis pointers)
- FHIR Condition resource created with:
  - Clinical status (active, resolved, inactive)
  - Verification status (confirmed, provisional, differential)
  - Code (ICD-10 and/or SNOMED)
  - Onset date
- CCDA export includes in Problem List section
- Search integrates with code tables (ICD-10, SNOMED)
- Audit log for problem add/edit/delete

**Technical Details:**
- **Location:** `/interface/patient_file/summary/add_edit_issue.php`
- **Database Table:** `lists` (type='medical_problem')
- **Code Tables:** `icd10_dx_order_code`, `sct_concepts` (SNOMED)
- **FHIR Controller:** `/src/RestControllers/FHIR/FhirConditionRestController.php`
- **Service:** `/src/Services/ConditionService.php`

---

### Feature 2.5: Medication List Management

**Description:**
Maintain current and historical medication list including active medications, discontinued medications, dosage, frequency, route, and prescriber information.

**How to Use (User Instructions):**
1. Open patient chart
2. Navigate to Medications section
3. Click "Add Medication"
4. Search for medication by:
   - Medication name
   - RxNorm code
5. Select medication from search results
6. Enter medication details:
   - Dosage (strength and dose)
   - Route (oral, IV, topical, etc.)
   - Frequency/schedule
   - Start date
   - Prescriber
   - Indication
   - Instructions
7. Click "Save"
8. Medication appears in active medication list
9. To discontinue: Edit medication, set end date and status to "Discontinued"

**How to Test:**
- **Test 1:** Add active medication with all fields, verify saved
- **Test 2:** Add medication via RxNorm code search
- **Test 3:** Discontinue medication, verify moves to historical list
- **Test 4:** Verify medication list displays in encounter
- **Test 5:** Export to FHIR, verify MedicationRequest resource created
- **Test 6:** Include in CCDA, verify in Medications section
- **Test 7:** Test drug-drug interaction checking if enabled
- **Test 8:** Test medication reconciliation workflow

**Expected System Behavior:**
- Medications stored in `lists` table where type='medication'
- RxNorm code stored if available (supports interoperability)
- Active medications shown prominently in chart
- Discontinued medications moved to historical section
- Medication list available in encounter for documentation
- FHIR export creates MedicationRequest resources with:
  - Medication code (RxNorm)
  - Dosage instruction
  - Status (active, stopped, completed)
  - Requester (prescriber)
- CCDA export includes in Medications section with RxNorm codes
- Integration with e-prescribing for medication history
- Drug interaction checking may trigger alerts
- Medication reconciliation compares external sources with current list

**Technical Details:**
- **Location:** `/interface/patient_file/summary/add_edit_issue.php` (type=medication)
- **Database Table:** `lists` (type='medication')
- **Drug Database:** RxNorm codes in `drug_inventory` or external sources
- **FHIR Controller:** `/src/RestControllers/FHIR/FhirMedicationRequestRestController.php`
- **Service:** Integration with `/src/Services/PrescriptionService.php`

---

### Feature 2.6: Allergy and Adverse Reaction Documentation

**Description:**
Document patient allergies and adverse reactions to medications, foods, environmental allergens, and other substances with reaction details and severity.

**How to Use (User Instructions):**
1. Open patient chart
2. Navigate to Allergies section
3. Click "Add Allergy"
4. Enter allergy information:
   - Allergen (medication, food, environmental, etc.)
   - Search for drug allergens by name
   - Reaction type (allergy, intolerance, sensitivity)
   - Reaction symptoms (rash, anaphylaxis, nausea, etc.)
   - Severity (mild, moderate, severe, life-threatening)
   - Onset date
5. Click "Save"
6. Allergy appears in allergy list with prominent warning display
7. Allergy alerts display when prescribing related medications

**How to Test:**
- **Test 1:** Add medication allergy, verify alert displays on prescribing
- **Test 2:** Add food allergy, verify appears in allergy list
- **Test 3:** Add environmental allergy, verify documented
- **Test 4:** Add "No known allergies" status, verify displays
- **Test 5:** Export to FHIR, verify AllergyIntolerance resource created
- **Test 6:** Include in CCDA, verify in Allergies section
- **Test 7:** Test allergy alert triggers during e-prescribing
- **Test 8:** Test severity indicators (life-threatening allergies highlighted)

**Expected System Behavior:**
- Allergies stored in `lists` table where type='allergy'
- "No known allergies" status can be documented
- Active allergies prominently displayed in chart header/banner
- Medication allergies trigger alerts during prescribing workflow
- Drug class checking (if penicillin allergy, alert on all penicillins)
- FHIR AllergyIntolerance resource created with:
  - Code (RxNorm for drugs, SNOMED for other allergens)
  - Reaction (manifestation and severity)
  - Clinical status (active, inactive, resolved)
  - Verification status (confirmed, unconfirmed)
- CCDA export includes in Allergies and Intolerances section
- Severity coding impacts clinical decision support

**Technical Details:**
- **Location:** `/interface/patient_file/summary/add_edit_issue.php` (type=allergy)
- **Database Table:** `lists` (type='allergy')
- **Alert Logic:** Checked during prescription creation
- **FHIR Controller:** `/src/RestControllers/FHIR/FhirAllergyIntoleranceRestController.php`
- **Service:** `/src/Services/AllergyIntoleranceService.php`

---

### Feature 2.7: Immunization Recording

**Description:**
Document administered vaccinations with vaccine type (CVX code), lot number, manufacturer, administration date, route, site, administered by, and VIS (Vaccine Information Statement) documentation.

**How to Use (User Instructions):**
1. Within encounter or Immunizations section
2. Click "Add Immunization"
3. Search and select vaccine by:
   - Vaccine name
   - CVX code
4. Enter administration details:
   - Administration date
   - Dose/amount
   - Route (IM, SubQ, oral, etc.)
   - Site (left deltoid, right thigh, etc.)
   - Lot number
   - Expiration date
   - Manufacturer
   - Administered by
5. Document VIS (Vaccine Information Statement):
   - VIS edition date
   - VIS given date
6. Record funding source (private, public/VFC)
7. Click "Save"
8. Immunization appears in immunization record

**How to Test:**
- **Test 1:** Record routine vaccination (e.g., influenza), verify all fields saved
- **Test 2:** Record pediatric vaccination series (e.g., DTaP series)
- **Test 3:** Verify CVX code lookup functionality
- **Test 4:** Test lot number and expiration tracking
- **Test 5:** Generate immunization registry report
- **Test 6:** Export to FHIR, verify Immunization resource created
- **Test 7:** Include in CCDA, verify in Immunizations section
- **Test 8:** Test integration with immunization registry (IIS) if configured
- **Test 9:** Test immunization reminder/forecast calculations

**Expected System Behavior:**
- Immunizations stored in `immunizations` table
- CVX codes (CDC vaccine codes) used for standardization
- Lot number tracking for recalls
- VIS documentation for compliance
- Immunization history displayed in chronological order
- Overdue immunization alerts based on ACIP schedules
- FHIR Immunization resource created with:
  - Vaccine code (CVX and NDC)
  - Occurrence date/time
  - Route, site, dose quantity
  - Lot number, expiration date
  - Performer (who administered)
  - VIS documentation
- CCDA export includes in Immunizations section
- Integration with state/local immunization registries via HL7
- Forecasting for next due immunizations

**Technical Details:**
- **Location:** `/interface/patient_file/immunizations.php`
- **Database Table:** `immunizations`
- **Code System:** CVX codes (vaccine), MVX codes (manufacturer)
- **FHIR Controller:** `/src/RestControllers/FHIR/FhirImmunizationRestController.php`
- **Service:** `/src/Services/ImmunizationService.php`
- **Registry Integration:** `/interface/modules/zend_modules/module/Immunization/`

---

### Feature 2.8: Mental Health Assessments (PHQ-9, GAD-7)

**Description:**
Administer and score standardized mental health assessment tools including PHQ-9 (depression), GAD-7 (anxiety), and other validated screening instruments.

**How to Use (User Instructions):**
1. Within encounter, add assessment form
2. Select form type:
   - PHQ-9 (Patient Health Questionnaire - Depression)
   - GAD-7 (Generalized Anxiety Disorder - 7 item)
3. Administer questionnaire (can be patient self-administered or provider-administered)
4. Enter responses for each question (0-3 scale typically)
5. System auto-calculates total score
6. Review score interpretation (minimal, mild, moderate, severe)
7. Document follow-up plan based on score
8. Click "Save"
9. Scores tracked over time for treatment monitoring

**How to Test:**
- **Test 1:** Complete PHQ-9, verify score auto-calculated correctly
- **Test 2:** Complete GAD-7, verify score and severity interpretation
- **Test 3:** Test all possible score ranges, verify interpretation accurate
- **Test 4:** View trending of scores over multiple encounters
- **Test 5:** Export to FHIR as Observation or QuestionnaireResponse
- **Test 6:** Verify scores trigger clinical decision support alerts if configured
- **Test 7:** Include in quality measure reporting (depression screening)
- **Test 8:** Test patient portal self-administration if available

**Expected System Behavior:**
- Assessment responses stored in form-specific tables (`form_phq9`, `form_gad7`)
- Total score auto-calculated based on validated algorithm
- Score interpretation displayed (e.g., PHQ-9: 0-4=minimal, 5-9=mild, 10-14=moderate, 15-19=moderately severe, 20-27=severe)
- Historical scores displayed for comparison
- May trigger clinical decision rules (e.g., high score triggers referral reminder)
- Can be exported as FHIR QuestionnaireResponse or Observation
- Scores available for quality measure reporting (CQM)
- Patient portal administration creates responses automatically
- Audit log for assessment completion

**Technical Details:**
- **PHQ-9 Location:** `/interface/forms/phq9/`
- **GAD-7 Location:** `/interface/forms/gad7/`
- **Database Tables:** `form_phq9`, `form_gad7`
- **Scoring:** Automated calculation on save
- **FHIR:** QuestionnaireResponse or Observation resources

---

### Feature 2.9: Social Determinants of Health (SDOH) Screening

**Description:**
Screen and document social determinants of health including food insecurity, housing instability, transportation needs, financial strain, and social isolation using standardized assessment tools.

**How to Use (User Instructions):**
1. Within encounter, add SDOH form
2. Administer screening questions covering:
   - Food security (hunger, food access)
   - Housing stability (housing insecurity, homelessness risk)
   - Transportation (reliable transportation for medical care)
   - Utilities (ability to pay for utilities)
   - Safety (intimate partner violence screening)
   - Social isolation
   - Financial strain
   - Employment/education
3. Document responses
4. Identify positive screens requiring intervention
5. Document referrals to social services
6. Click "Save"
7. SDOH data available for population health management

**How to Test:**
- **Test 1:** Complete SDOH screening, verify all domains captured
- **Test 2:** Test positive screens trigger referral workflows
- **Test 3:** Export to FHIR as Observation or QuestionnaireResponse
- **Test 4:** Include in CCDA Social History section
- **Test 5:** Generate SDOH reports for population health
- **Test 6:** Test Z-code documentation (ICD-10 Z codes for social determinants)
- **Test 7:** Verify patient portal display/administration if available

**Expected System Behavior:**
- SDOH data stored in `form_sdoh` table or as Observations
- Positive screens flagged for provider review
- ICD-10 Z-codes can be assigned for billing (Z59.0 homelessness, Z59.4 food insecurity, etc.)
- FHIR Observation resources created with LOINC codes for SDOH
- CCDA Social History section populated
- Reports available for population health initiatives
- Integration with care coordination for referrals
- Data supports health equity initiatives
- Audit logging

**Technical Details:**
- **Location:** `/interface/forms/sdoh/`
- **Database Table:** `form_sdoh`
- **Code Systems:** LOINC codes for SDOH, ICD-10 Z codes
- **FHIR:** Observation resources with SDOH category
- **Standards:** Gravity Project SDOH vocabulary

---

### Feature 2.10: Layout-Based Form Builder (Custom Forms)

**Description:**
Visual form designer allowing creation of unlimited custom forms with drag-and-drop field placement, custom data types, conditional logic, and database binding for specialty-specific documentation needs.

**How to Use (User Instructions):**

**Creating a Custom Form:**
1. Navigate to Administration → Forms → Layout Editor
2. Click "New Layout"
3. Enter form metadata:
   - Form name/title
   - Group/category
   - Active status
4. Add fields to form:
   - Drag field types from palette (text, date, select, checkbox, etc.)
   - Configure field properties (label, data type, options, validation)
   - Set field position and size
   - Define conditional display rules
   - Map field to database column
5. Configure data source (new table or existing table)
6. Save form layout
7. Form becomes available in encounter "Add Form" menu

**Using a Custom Form:**
1. Within encounter, click "Add Form"
2. Select custom form from list
3. Complete form fields
4. Click "Save"
5. Data stored and queryable

**How to Test:**
- **Test 1:** Create simple custom form with text fields, verify appears in form list
- **Test 2:** Create form with select list, verify options display correctly
- **Test 3:** Create form with conditional logic (show field B only if field A = "Yes")
- **Test 4:** Create form mapping to new database table, verify table created
- **Test 5:** Create form mapping to existing table, verify data stored
- **Test 6:** Complete custom form in encounter, verify data saved
- **Test 7:** Query custom form data in reports
- **Test 8:** Export custom form data via API

**Expected System Behavior:**
- Form layouts stored in `layout_options` table
- Form metadata in `list_options` where list_id='lists'
- Custom data tables created automatically if specified
- Field types include: text, textarea, date, datetime, select, multiselect, radio, checkbox, provider, facility, billing code, etc.
- Conditional display logic evaluated client-side (JavaScript)
- Data validation applied based on field type
- Custom forms integrated into encounter workflow
- Form data accessible via service layer
- Forms can be version-controlled
- ACL applies to form access
- Audit logging for form completion

**Technical Details:**
- **Layout Editor:** `/interface/super/edit_layout.php`
- **Form Handler:** `/interface/forms/LBF/`
- **Database Tables:** `layout_options`, `list_options`, custom data tables
- **Field Types:** 30+ predefined data types
- **Validation:** Client-side and server-side
- **Extensibility:** New field types can be added

---

## Configuration

### Global Settings

**Location:** Administration → Globals → Appearance / Features

- **Encounter Numbering** - Format for encounter IDs
- **Encounter Locking** - Auto-lock encounters after billing
- **Default Encounter Provider** - Auto-populate provider from user
- **Form Auto-Save** - Auto-save form data periodically
- **Problem List Display** - Show ICD-10, SNOMED, or both
- **Medication List Source** - RxNorm integration
- **Allergy Alerts** - Enable prescribing alerts
- **Immunization Forecasting** - Enable ACIP schedule-based forecasting
- **SDOH Screening** - Enable SDOH documentation
- **Custom Form Access** - ACL for custom forms

### ACL Requirements

- **Encounter Create** - `encounters/auth` - Create encounters
- **Encounter View** - `encounters/notes` - View encounter documentation
- **Encounter Edit** - `encounters/auth/write` - Edit encounters
- **Problem List** - `patients/med` - Manage problem list
- **Medication List** - `patients/med` - Manage medications
- **Allergy List** - `patients/med` - Manage allergies
- **Immunizations** - `patients/med` or specific immunization ACL

---

## Related Capabilities

- **[Patient Management](01-patient-management.md)** - Patient context for documentation
- **[Billing and Revenue Cycle](04-billing-revenue-cycle.md)** - Encounter billing and coding
- **[Pharmacy and Prescriptions](06-pharmacy-prescriptions.md)** - Medication prescribing
- **[Clinical Decision Support](12-clinical-decision-support.md)** - Alerts and reminders based on documentation
- **[Health Information Exchange](08-health-information-exchange.md)** - FHIR/CCDA export of clinical data
- **[Reporting and Analytics](07-reporting-analytics.md)** - Clinical data reporting

---

## Compliance and Standards

- **FHIR R4** - Clinical resources (Condition, Observation, Procedure, MedicationRequest, AllergyIntolerance, Immunization)
- **CCDA 2.1** - All clinical sections (Problems, Medications, Allergies, Immunizations, Vitals, Encounters)
- **LOINC** - Standardized observation codes
- **SNOMED CT** - Clinical terminology
- **ICD-10-CM** - Diagnosis codes
- **RxNorm** - Medication codes
- **CVX** - Vaccine codes
- **ONC Certification** - Clinical documentation requirements
- **Meaningful Use** - Clinical quality measures based on documentation

---

**End of Clinical Documentation Capability**
