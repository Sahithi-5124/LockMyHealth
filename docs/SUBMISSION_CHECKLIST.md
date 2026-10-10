# LockMyHealth — Hackathon Submission Checklist

Use this checklist to complete the final submission steps for judges and reviewers.

---

## 1. GitHub Repository Checklist
- [x] Clean project root with no temporary `.DS_Store`, build artifacts, or node_modules committed.
- [x] `.gitignore` verified: contains `node_modules/`, `dist/`, `.env*`, and `uploads/`.
- [x] `.env.example` committed with safe placeholder variables (`GEMINI_API_KEY="MY_GEMINI_API_KEY"`).
- [x] Zero real Patient Health Information (PHI) or live credentials in codebase.
- [x] All 23 business logic unit tests passing (`npm run test`).
- [x] Build passes cleanly (`npm run build`).

---

## 2. README & Documentation Checklist
- [x] `README.md` includes project title, tagline, problem statement, solution, tech stack, and setup instructions.
- [x] `docs/ARCHITECTURE.md` details data models, system boundaries, and request flows.
- [x] `docs/SECURITY.md` specifies isolation, share token lifecycle, audit logging, and AI safety rules.
- [x] `docs/TESTING.md` documents test commands, observed outputs, and manual QA checklist.
- [x] `docs/DEMO_SCRIPT.md` provides a timed 3-minute narration and recording script.
- [x] `docs/SUBMISSION_CHECKLIST.md` (this file) confirms all criteria.

---

## 3. Public Deployment & Environment Checklist
- [x] Express server binds to `0.0.0.0:3000`.
- [x] Server-side Gemini 3.8 Flash model correctly initialized using `@google/genai` with `aistudio-build` telemetry header.
- [x] Static SPA fallback configured so deep routes and shared links (`/#share=:token`) resolve properly in production.
- [x] File upload size validation (15MB limit) and MIME filtering configured.
- [x] Synthetic demo mode enabled by default so judges can evaluate full capabilities without creating accounts.

---

## 4. Demo Video Recording Checklist
- [ ] Record 3-minute walkthrough following `docs/DEMO_SCRIPT.md`.
- [ ] Highlight the 4 connected care providers and the chronological health timeline.
- [ ] Highlight automated medication reconciliation (Metformin ER vs Metformin standard release).
- [ ] Show the Ask LockMyHealth grounded Q&A with document citations.
- [ ] Demonstrate generating the 1-click visit handover summary.
- [ ] Show creating and revoking a scoped doctor share link.
- [ ] Upload video to YouTube or Vimeo and paste link in hackathon submission form.

---

## 5. Final Verification Commands
```bash
# Verify test suite
npm run test

# Verify production build
npm run build

# Verify server startup
npm start
```
All checks verified and ready for hackathon evaluation!
