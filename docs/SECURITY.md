# LockMyHealth — Security & Privacy Architecture

## 1. Data Isolation & Multi-Tenancy

* **Strict User Partitioning:** Every database entity (documents, medications, timeline events, appointments, share grants) is keyed and isolated by `userId`.
* **Server-Side Enforcement:** Frontend state is never trusted as a security boundary. All API endpoints validate the session/Bearer token before reading or modifying data.
* **No Cross-User Parameter Tampering:** Changing a document ID or URL parameter will never return records belonging to another patient account. Attempting to query an unauthorized record returns a 404/403.

---

## 2. Share-Token Expiry & Instant Revocation

* **Unguessable Tokens:** Share links use 128-bit cryptographically secure pseudorandom tokens (`mt_share_` + 32 hex characters generated via `crypto.randomBytes(16)`).
* **Deterministic Server-Side Validation:**
  ```ts
  if (foundGrant.isRevoked) return { status: 'revoked' };
  if (new Date() > new Date(foundGrant.expiresAt)) return { status: 'expired' };
  ```
* **Strict Scope Filtration:** The public share resolver `/api/share/public/:token` delivers only records that match the patient's explicit category filter. Records outside the scope are excluded server-side.
* **Audit Logging:** Every read access is recorded in `shareAuditLogs` with:
  - Timestamp
  - Masked IP (last octets redacted: `192.168.***.***`)
  - Truncated client user-agent snippet
* **Copy Limitation Transparency:** The application clearly warns patients that while revocation immediately blocks subsequent online access, it cannot erase copies or prints a recipient made while the link was active.

---

## 3. Secret Management & API Protection

* **Zero Frontend Exposure:** The Gemini API key (`process.env.GEMINI_API_KEY`) is kept strictly server-side. No client bundle or network payload exposes the secret.
* **Telemetry User-Agent:** All server calls to `@google/genai` explicitly include the header `'User-Agent': 'aistudio-build'` via `httpOptions`.
* **No Sensitive Info in Logs:** Server error logs do not write raw access tokens or medical diagnoses to stdout.

---

## 4. File Upload Validation & Storage Security

* **MIME-Type Whitelisting:** Only `application/pdf`, `image/jpeg`, `image/jpg`, and `image/png` are accepted. Executables, scripts, and HTML are rejected.
* **Strict Size Limit:** File payloads exceeding 15MB are rejected by Multer.
* **Sanitized File Names:** Uploaded files have special characters stripped to prevent directory traversal attacks (`file.originalname.replace(/[^a-zA-Z0-9._-]/g, '_')`).
* **Structured Storage:** Medical documents are stored as distinct filesystem assets rather than unstructured base64 strings embedded in database records.

---

## 5. AI Safety & Clinical Decision Boundaries

* **Informational & Non-Prescriptive:** LockMyHealth explicitly declares that AI outputs are record-retrieval summaries and not qualified clinical advice.
* **No Autonomous Dose Alterations:** The model is prohibited from recommending starting, stopping, or altering medications.
* **Emergency Redirection:** Prompts mentioning life-threatening symptoms (e.g. acute chest pressure, stroke symptoms, uncontrolled bleeding) trigger an immediate direction to contact local emergency medical services.
* **Unverified Status by Default:** All AI-extracted facts are flagged as unverified until explicitly reviewed and confirmed by the patient.

---

## 6. Remaining Limitations & Production Recommendations

* **Production Encryption at Rest:** In production deployments, files in the `uploads/` volume should be encrypted using AES-256 with envelope encryption.
* **TLS / HTTPS:** All transport must use TLS 1.3 in production to protect transit integrity.
* **Regulatory Disclaimer:** LockMyHealth is a hackathon continuity demonstration using synthetic patient records. Production clinical deployment requires HIPAA/GDPR compliance reviews and institutional BAA agreements.
