# LockMyHealth — Architecture & System Design

## 1. System Architecture Overview

LockMyHealth is architected as a full-stack, secure, decoupled healthcare continuity application. The frontend is built with React 19 and Tailwind CSS, while the backend is an Express Node.js application running in TypeScript. 

```
┌────────────────────────────────────────────────────────┐
│                   Client Browser                       │
│  - React 19 SPA (Vite / Tailwind CSS)                  │
│  - Responsive Navigation (10 Core Modules)             │
│  - Emergency Card & Printable Visit Handover           │
│  - Read-Only Clinician Viewer (/#share=:token)         │
└──────────────────────────┬─────────────────────────────┘
                           │ HTTP / JSON API (Bearer Token)
┌──────────────────────────▼─────────────────────────────┐
│                 Node.js / Express Server               │
│  - server.ts: API Endpoints, Auth & Access Validation  │
│  - Multer: 15MB Upload Filter (PDF, JPG, PNG)          │
│  - db.ts: User Data Isolation & Persistence            │
│  - ai.ts: Grounded Gemini 3.8 Flash RAG Service        │
└──────────────┬──────────────────────────┬──────────────┘
               │                          │
┌──────────────▼───────────┐  ┌───────────▼──────────────┐
│  Persistent Vault Store  │  │ Google GenAI SDK Backend │
│  - data/LockMyHealth_data   │  │  - gemini-3.8-flash      │
│  - uploads/ (Files)      │  │  - User-Agent Telemetry  │
│  - Synthetic Seed Engine │  │  - Source Grounding RAG  │
└──────────────────────────┘  └──────────────────────────┘
```

---

## 2. Data Model

The data model is formalized in `src/types/medical.ts` and managed through `server/db.ts`:

1. **User Profile (`UserProfile`):**
   - `id`, `name`, `email`, `age`, `gender`, `bloodGroup`, `emergencyContact`, `isDemo`
2. **Provider (`Provider`):**
   - `id`, `name`, `role`, `specialty`, `facility`, `phone`, `email`, `address`, `color`
3. **Medical Document (`MedicalDocument`):**
   - `id`, `userId`, `title`, `category`, `providerId`, `providerName`, `documentDate`, `uploadedAt`, `fileUrl`, `fileName`, `fileSize`, `fileMimeType`, `primaryCondition`, `tags`, `notes`, `extractedData`, `isVerified`, `ocrTextPreview`
4. **Timeline Event (`TimelineEvent`):**
   - `id`, `userId`, `date`, `type`, `title`, `description`, `providerId`, `providerName`, `documentId`, `condition`, `sourceType` (`extracted` | `patient_entered` | `confirmed` | `ai_explanation`)
5. **Medication Record (`MedicationRecord`):**
   - `id`, `userId`, `name`, `dose`, `frequency`, `instructions`, `providerId`, `providerName`, `documentId`, `startDate`, `endDate`, `status` (`needs_review` | `active_confirmed` | `historical` | `discontinued_confirmed`), `condition`, `patientNotes`
6. **Medication Conflict (`MedicationConflict`):**
   - `id`, `type` (`duplicate` | `dose_mismatch` | `status_conflict` | `unclear_instructions`), `medicationName`, `records`, `description`, `clinicalPrompt`, `severity`
7. **Lab Test Record (`LabTestRecord`):**
   - `id`, `userId`, `testName`, `category`, `value`, `numericValue`, `unit`, `referenceRange`, `isAbnormal`, `testDate`, `providerId`, `providerName`, `documentId`, `aiExplanation`
8. **Appointment & Reminder (`Appointment`):**
   - `id`, `userId`, `title`, `providerId`, `providerName`, `date`, `time`, `timeZone`, `type`, `status`, `location`, `reason`, `checklist`, `reminderEnabled`
9. **Share Grant (`ShareGrant`):**
   - `id`, `token` (`mt_share_...`), `userId`, `label`, `recipientName`, `recipientRole`, `allowedCategories`, `createdAt`, `expiresAt`, `isRevoked`, `accessCount`, `lastAccessedAt`
10. **Share Audit Log (`ShareAuditLog`):**
    - `id`, `grantId`, `token`, `timestamp`, `clientIpMasked`, `userAgentSnippet`

---

## 3. Request & Data Flow

1. **Manual Document Upload:**
   - User selects a PDF or image; the server validates MIME type and file size (under 15 MB).
   - The user enters or edits the title, category, provider, date, condition, and notes.
   - The record is stored in the user's vault and a timeline event is emitted.

2. **Medication Reconciliation Flow:**
   - On every fetch of `/api/medications/reconciliation`, the reconciliation engine parses all stored medication records for the user.
   - Normalizes generic names and detects:
     * Simultaneous active entries with differing doses (e.g. Telmisartan 40mg vs 80mg).
     * Missing or ambiguous instructions.
     * Transitions from baseline to active formulations.
   - Generates non-prescriptive clinical prompts for doctor review.

3. **Patient-Controlled Sharing Flow:**
   - Patient specifies recipient, scope (allowed categories), and expiry duration (e.g., 72 hours).
   - Server generates a 128-bit cryptographically secure token (`crypto.randomBytes(16).toString('hex')`).
   - Doctor opens `/#share=<token>`.
   - Server checks if token is expired or revoked.
   - If active, records client timestamp, masked IP, and user-agent in audit log.
   - Returns strictly the scoped records without revealing user credentials or unpermitted documents.

---

## 4. AI Retrieval and Source-Grounding Design

In `server/ai.ts`, the **Ask LockMyHealth** chatbot operates using a grounded retrieval pipeline:

1. **Context Assembly:** Upon receiving a query, the backend retrieves only the authenticated user's documents, medications, conflicts, lab results, and appointments.
2. **Context Compression:** Builds a structured clinical markdown ledger containing document IDs, dates, prescriber names, and test reference ranges.
3. **Safety & Grounding Instructions:** The Gemini 3.8 Flash system instruction mandates:
   - Cite source documents and dates (`[Doc: Endocrine Review (2026-08-14)]`).
   - Never diagnose or prescribe.
   - Explicitly highlight conflicting information rather than guessing the "correct" dose.
   - Direct acute emergency symptoms (chest pain, severe dyspnea) immediately to emergency services (911).
   - When evidence is absent, state: *"I don't find this information in your uploaded LockMyHealth records."*

---

## 5. Deployment Architecture

- **Development:** tsx executes `server.ts`, which mounts Vite middlewares in dev mode on port 3000.
- **Production:** `npm run build` generates optimized client bundles into `dist/`. The Express server serves `dist/` static files with single-page application fallback routing.
