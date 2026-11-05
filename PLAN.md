# MediExplain AI — AI Medical Report Explainer: Implementation Plan

## 1. Project Architecture Overview
MediExplain AI is a full-stack medical report explainer built with:
- **Frontend**: React 18 / Vite, Tailwind CSS, Lucide React icons, React Router v6, Axios, custom RTL Urdu typography support.
- **Backend**: Node.js v24, Express, MongoDB (Mongoose), JWT authentication with HttpOnly cookies + Bearer token support, Multer for private file uploads, Express Rate Limit, Helmet, CORS.
- **Document & OCR Services**:
  - `pdfService.js`: Direct text extraction for digital PDFs (`pdf-parse`).
  - `ocrService.js`: Image OCR using `tesseract.js` for images (PNG, JPG) and scanned document pages.
  - `reportExtractionService.js`: Heuristic/regex parser for lab tests (Hemoglobin, WBC, Platelets, Fasting Glucose, HbA1c, Cholesterol, Creatinine, Bilirubin, etc.) extracting value, unit, reference range, and status.
- **AI Service**:
  - `aiExplanationService.js`: Pluggable LLM service supporting Google Gemini API, OpenAI API, or Local/Offline Fallback Engine when API key is missing or offline, ensuring 100% operational reliability for evaluation. Validates structured JSON schema for English and Urdu explanations.
- **Security & Privacy**: Private storage folder outside web root, file magic number & extension validation, report ownership checks, cascade user deletion, rate limiting, and zero client-exposed secrets.

---

## 2. Implementation Phases

### Phase 1: Environment Setup & Project Scaffolding
- Initialize git repository.
- Setup monorepo/sub-directory structure: `client/` and `server/`.
- Backend initialization (`package.json`, install `express`, `mongoose`, `cors`, `cookie-parser`, `dotenv`, `bcryptjs`, `jsonwebtoken`, `multer`, `helmet`, `express-rate-limit`, `pdf-parse`, `tesseract.js`, `zod`/custom validator).
- Frontend initialization with Vite React template, install Tailwind CSS, Lucide icons, `axios`, `react-router-dom`.
- Configure `.env.example` and development scripts.

### Phase 2: Backend Architecture & Core Services
- Database Connection (`config/db.js`).
- Models:
  - `User.js`: `name`, `email`, `passwordHash`, timestamps.
  - `MedicalReport.js`: `userId`, `originalFileName`, `mimeType`, `fileSize`, `filePath`, `extractedText`, `reportDate`, `processingStatus`, `extractedResults`, `aiExplanation`, `language`, timestamps.
- Security & Middleware:
  - JWT auth middleware (`authenticateToken`).
  - File upload middleware with Multer and MIME/extension filtering (`private_uploads/`).
  - Rate limiters for auth and upload endpoints.
  - Centralized error handler.
- Services:
  - `fileStorageService.js`: Safe file deletion, sanitization, private file access.
  - `pdfService.js`: Text extraction from digital PDFs.
  - `ocrService.js`: OCR extraction from PNG/JPG/JPEG using Tesseract.js.
  - `reportExtractionService.js`: Rule-based lab extraction & range analysis.
  - `aiExplanationService.js`: LLM caller with robust JSON schema validation, English and Urdu prompts, and graceful offline fallback engine.
- Controllers & Routes:
  - Auth: `/api/auth/register`, `/api/auth/login`, `/api/auth/logout`, `/api/auth/me`.
  - Reports: `/api/reports/upload`, `/api/reports`, `/api/reports/:id`, `/api/reports/:id/explain`, `/api/reports/:id` (DELETE).
  - Profile: `/api/users/profile`, `/api/users/password`, `/api/users/account` (DELETE with cascade).
  - Sample/Demo: `/api/reports/sample-demo` for 1-click evaluator testing.

### Phase 3: Frontend Implementation & UX Design
- Tailwind design system: Medical SaaS aesthetic (light slate/sky background `#F8FAFC`, deep navy text `#0F172A`, teal `#0D9488`, cyan/blue `#0284C7`, emerald for normal, amber for warning, rose for high/low).
- Language Context / Switcher (English and Urdu with RTL support, proper Urdu fonts like Noto Nastaliq Urdu / Noto Sans Arabic fallbacks).
- Auth Context & Axios API client with automatic token & cookie handling.
- Components:
  - Navbar with active status, language selector, user profile dropdown.
  - Footer with disclaimer, links, college project notice.
  - StatusBadge, Modal, ConfirmDialog, StatCard, LoadingSpinner, NotificationBanner.
  - ResultsTable with High/Low/Normal/Unclear indicators, range bar visualization, uncertainty notes.
- Pages:
  - Landing Page (Hero, 3-step workflow, features, privacy guarantees, medical disclaimer, FAQs).
  - Login & Register pages with client & server validation.
  - User Dashboard (quick stats, recent reports, upload CTA, responsible usage guide).
  - Report Upload Page (drag-and-drop, sample demo report loader, validation, real-time pipeline status).
  - Report Detail / Results Page (Overview, Categorized results, English/Urdu explanation cards, questions to ask your doctor, original extracted text inspection, print/download report).
  - Report History Page (all reports, search/filter, status, view, delete modal).
  - User Profile & Settings Page (account info, password change, data retention policy, cascade delete account).
  - About, Privacy, and Medical Disclaimer static informational pages.

### Phase 4: Sample Data & Demonstration Materials
- Sample lab reports:
  - Complete Blood Count (CBC) and Metabolic Panel report containing fictional values (Hemoglobin, Fasting Blood Glucose, Serum Creatinine, Total Cholesterol, WBC, Platelets).
  - Watermarked with: "DEMO DATA — NOT A REAL PATIENT REPORT".
  - Fictional PDF generator script.

### Phase 5: Testing & Verification
- Unit & integration tests for:
  - Auth registration, valid/invalid login, token verification.
  - File upload restrictions (accept valid pdf/png/jpg, reject exe/txt, reject > 10MB).
  - PDF & OCR extraction pipeline.
  - Lab test heuristic extraction.
  - AI JSON validation and offline fallback.
  - Ownership security: User B cannot access User A's reports.
  - Report deletion and account cascade deletion.
  - Frontend responsiveness and loading states.

### Phase 6: College Submission Package & Documentation
- Comprehensive README.md covering all 23 required points.
- Project Abstract, Problem Statement, Objectives, Module Descriptions.
- Mermaid System Architecture & Data Flow Diagrams.
- Evaluator Walkthrough & Testing Checklist.
