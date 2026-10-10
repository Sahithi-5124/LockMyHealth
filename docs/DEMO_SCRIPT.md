# LockMyHealth — 3-Minute Hackathon Demo Video Script

**Target Video Length:** ~3 minutes (180 seconds)  
**Presenter Tone:** Professional, empathetic, clear, engineering-focused  
**Target Audience:** Hackathon Judges, Clinicians, Healthcare Engineers  

---

### [0:00 – 0:30] Problem Hook & The Healthcare Fragmentation Crisis
* **Visual:** Open on a paper pile or slide showing fragmented clinic receipts, test reports, and pill bottles, then cut to the clean **LockMyHealth Command Center Overview**.
* **Narration:**
  > "Meet Rajesh Kumar, a 58-year-old patient managing Type 2 diabetes and hypertension. Over the last year, Rajesh has visited four different clinics and diagnostic labs.
  > Like millions of chronic care patients, his prescriptions, blood tests, and physician notes are scattered across paper files and portal logins.
  > When Rajesh visits a new specialist or emergency room, clinicians lack a single unified timeline. The result? Repeated lab draws, missed drug interactions, and interrupted continuity of care.
  > This is **LockMyHealth** — your health, connected."

---

### [0:30 – 1:15] Centralized Health Vault & My Doctors Folder System
* **Visual:** Navigate to **"Overview"**, point out the 4 connected care providers, then click into **"My Doctors"**. Select **Dr. Kavya Rao (Endocrinologist)** and **Dr. Ananya Sharma (General Physician)**. Show filed records.
* **Narration:**
  > "LockMyHealth organizes patient history into dedicated provider folders without creating duplicate copies.
  > Here, Dr. Ananya Sharma's clinic notes sit alongside Dr. Kavya Rao's endocrine reviews and Greenfield Pathology results.
  > Every document retains its original source file, upload timestamp, and OCR text.
  > When we upload a new prescription, our server-side **Gemini 3.8 Flash** engine extracts structured dosages, test values, and follow-up dates in seconds. Crucially, all extracted medical facts are treated as unverified until reviewed and confirmed by the patient."

---

### [1:15 – 1:55] Medication Reconciliation & Serial Lab Comparisons
* **Visual:** Navigate to **"Medications"**. Highlight the amber alert banner. Show the conflict detection for Metformin (transition from 500mg BID to 1000mg ER) and Telmisartan. Then click into **"Lab Results"** and demonstrate the interactive HbA1c trend chart (8.2% down to 6.8%).
* **Narration:**
  > "One of our most powerful features is automated **Medication Reconciliation**.
  > Rather than blindly trusting outdated prescriptions, LockMyHealth’s reconciliation engine detects dosage shifts and simultaneous prescriptions across different doctors.
  > It alerts Rajesh with a clinical review prompt: 'Verify with Dr. Rao whether your older 500mg Metformin should be officially marked discontinued.'
  > Over in **Lab Results**, serial tests like HbA1c and fasting blood sugar are graphed chronologically against verified reference ranges, with 1-click AI explanations that explain what each biomarker means."

---

### [1:55 – 2:30] Grounded AI Assistant (Ask LockMyHealth) & Visit Preparation
* **Visual:** Navigate to **"Ask LockMyHealth"**. Click the prompt *"What did my last prescription say?"*. Show the streaming response citing Dr. Rao's August 14 prescription. Next, click **"Prepare for Visit"** in the top bar to display the printable 1-click handover briefing.
* **Narration:**
  > "Patients often have questions between visits. **Ask LockMyHealth** uses grounded retrieval on the server to answer queries using *only* the patient's verified vault records.
  > Notice how the answer cites the exact supporting document: Dr. Rao's August prescription. The AI never guesses dosages or diagnoses.
  > And before Rajesh walks into his next appointment, he clicks **Prepare for Visit**.
  > LockMyHealth compiles a structured 1-page clinical handover — active medications, confirmed Penicillin allergies, recent labs, and patient-saved questions — ready to print or save as a PDF."

---

### [2:30 – 3:00] Revocable Controlled Sharing & Technical Highlights
* **Visual:** Navigate to **"Share Records"**. Click **"Create New Share Link"**, select 72 hours and 'Prescriptions only'. Copy the token and open `/#share=<token>` in a new tab to show the clean, read-only clinician view. Return and click **"Revoke Access"**. Show the audit log.
* **Narration:**
  > "Finally, patient privacy is paramount.
  > Instead of sharing an entire account, Rajesh creates an unguessable 128-bit share token scoped only to prescriptions, expiring in 72 hours.
  > Consulting doctors see a clean, read-only briefing, and every access is logged in the audit trail. With one tap, Rajesh can revoke access at any time.
  > Built with React 19, TypeScript, and server-side Gemini 3.8 Flash, LockMyHealth puts patients in control of their health continuity.
  > Thank you."

---

### End of Video Checklist for Presenter
- [ ] Record in 1080p 60fps or 30fps with clear audio.
- [ ] Ensure synthetic patient name "Rajesh Kumar" is clearly visible.
- [ ] Demonstrate both the patient dashboard and the doctor view (`/#share=token`).
- [ ] Save video as `demo-video.mp4` or upload to YouTube/Vimeo as an unlisted link and link in `README.md`.
