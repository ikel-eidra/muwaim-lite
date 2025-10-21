# MUWAIM Lite v1.1 - ATS CV Tailoring System

**Arabic:** موائم - موائم سيرتك مع الوظيفة
**Tagline:** Tailor Your CV to the Job

## Overview

MUWAIM is a freemium mobile-first application designed to help jobless and under-resourced applicants optimize their CVs for Applicant Tracking Systems (ATS). The app analyzes job descriptions, scores CV compatibility, and generates tailored resumes with cover letters—all while maintaining strict data privacy through ephemeral storage.

### Core Mission
> **No one should pay just to get hired.**

MUWAIM provides essential CV tailoring features for free, with optional Pro features for power users.

## Features

### Free Tier (Permanent)
- ✅ Upload 1 CV (PDF/DOCX)
- ✅ Paste 1 job description OR search 3 jobs
- ✅ Instant ATS scoring (0-100 with detailed reasons)
- ✅ CV rewrite with user-consent changes only
- ✅ Generate Resume + Cover Letter (PDF & DOCX)
- ✅ Download all files immediately
- ✅ Manual apply via email intent
- ✅ Supported by non-intrusive ads

### Pro Tier (₱99/week or SAR 9.99/week)
- ✨ Unlimited CV rewrites (up to 10 jobs at once)
- ✨ Bulk cover letter generation
- ✨ Export all as ZIP + CSV report
- ✨ Premium ATS-optimized templates
- ✨ Ad-free experience

## Trust Architecture v1.0

MUWAIM implements strict privacy and data handling:

- **Ephemeral Storage:** All uploaded files and generated documents are automatically deleted after 24 hours
- **No Data Harvesting:** Zero user data collection or profiling
- **No-Retain LLM:** All AI API calls include explicit no-retain/no-train instructions
- **Logs:** Contain only non-PII metadata (timestamps, function names, error codes)
- **In-App Badge:** Displays "🛡️ Data deleted after 24 hours"

## Technology Stack

### Mobile App
- **Framework:** Expo (React Native) SDK 54
- **Language:** TypeScript
- **State Management:** Zustand
- **i18n:** i18next (English & Arabic with RTL support)
- **Navigation:** Expo Router (tabs-based)

### Backend
- **Serverless:** Supabase Edge Functions (Deno)
- **Database:** Supabase PostgreSQL (ephemeral storage only)
- **AI:** OpenAI GPT-4o-mini for CV parsing & optimization
- **Storage:** Supabase Storage with 24h auto-purge

### Key Libraries
- `expo-document-picker` - CV upload
- `expo-file-system` - File handling
- `expo-mail-composer` - Email application
- `expo-sharing` - Download/share documents
- `lucide-react-native` - Icons
- `@supabase/supabase-js` - Backend integration

## Architecture

### Data Flow
```
1. User uploads CV → cv-parse function → Extract text
2. User searches jobs → jobs-search function → Return 10 jobs
3. System calculates match → ats-score function → Score 0-100
4. User selects job → ats-align function → Optimize CV
5. System generates docs → doc-render function → PDF/DOCX files
6. User downloads → Ephemeral storage → Auto-delete in 24h
```

### Edge Functions (Stateless)

1. **cv-parse**
   - Input: PDF/DOCX file (base64)
   - Output: Plain text + structure analysis

2. **jobs-search**
   - Input: Keyword, location, remote flag
   - Output: 10 jobs from public APIs (Greenhouse, Lever, Workable)

3. **ats-score**
   - Input: CV text + JD text
   - Output: Score (0-100) + reasons + suggested fixes
   - Rubric: Keywords (35), Semantic (25), Structure (20), Action (10), Hygiene (10)

4. **ats-align**
   - Input: CV text + target JD/job + constraints
   - Output: Optimized resume markdown + changes array + final score
   - Constraint: No fabrication of employers/titles/dates

5. **cover-letter**
   - Input: CV text + job + tone
   - Output: Cover letter markdown

6. **doc-render**
   - Input: Resume markdown + cover markdown + job + score
   - Output: URLs for PDF/DOCX files + optional ZIP + CSV report

## Project Structure

```
muwaim-lite/
├── app/                          # Expo Router app
│   ├── (tabs)/                   # Tab navigation
│   │   ├── index.tsx             # Home/Upload screen
│   │   ├── search.tsx            # Job search screen
│   │   ├── matches.tsx           # ATS match scores screen
│   │   ├── tailor.tsx            # CV tailoring screen
│   │   ├── downloads.tsx         # Download documents screen
│   │   └── settings.tsx          # Settings screen
│   └── _layout.tsx               # Root layout
├── lib/                          # Shared libraries
│   ├── i18n.ts                   # i18n configuration
│   ├── api.ts                    # API client
│   ├── store.ts                  # Zustand state management
│   ├── supabase.ts               # Supabase client
│   └── locales/                  # Translation files
│       ├── en.json               # English translations
│       └── ar.json               # Arabic translations
├── supabase/
│   ├── functions/                # Edge Functions
│   │   ├── cv-parse/
│   │   ├── jobs-search/
│   │   ├── ats-score/
│   │   ├── ats-align/
│   │   ├── cover-letter/
│   │   └── doc-render/
│   └── storage-purge.sql         # 24h auto-purge script
├── docs/                         # Documentation
│   ├── README.md                 # This file
│   ├── DEPLOY_ANDROID.md         # Android deployment guide
│   ├── DEPLOY_WEB.md             # Web deployment guide
│   ├── PRIVACY.md                # Privacy policy
│   └── ADS_IAP.md                # AdMob & IAP setup
├── app.json                      # Expo configuration
├── eas.json                      # EAS Build configuration
├── package.json                  # Dependencies
└── tsconfig.json                 # TypeScript configuration
```

## Getting Started

### Prerequisites
- Node.js 18+
- npm or yarn
- Expo CLI: `npm install -g expo-cli`
- EAS CLI: `npm install -g eas-cli`
- Supabase project with Edge Functions enabled

### Installation

1. Clone the repository
2. Install dependencies:
   ```bash
   npm install
   ```

3. Configure environment variables in `.env`:
   ```
   EXPO_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
   EXPO_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
   ```

4. Start development server:
   ```bash
   npm run dev
   ```

### Deployment

See detailed guides:
- [Android Deployment](./DEPLOY_ANDROID.md)
- [Web Deployment](./DEPLOY_WEB.md)

## Testing Acceptance Criteria

✅ **Search Test:** Search "civil engineer riyadh" returns 10 jobs in ≤ 8s
✅ **Match Test:** Upload CV + select job → ATS score displayed with reasons
✅ **Tailor Test:** Optimized resume generated with user-consent changes only
✅ **Download Test:** PDF + DOCX files for resume and cover letter
✅ **Apply Test:** Email intent opens with pre-filled subject/body + attachments
✅ **i18n Test:** EN/AR toggle switches language and mirrors layout (RTL)
✅ **Ads Test:** AdMob banner/interstitial displays within limits
✅ **IAP Test:** Pro upgrade prompt visible (mock in dev, real in production)
✅ **Trust Test:** 24h purge job runs successfully

## License

Proprietary - All rights reserved

## Support

For issues, feature requests, or questions, contact: support@muwaim.app

---

**Built with ❤️ for jobseekers worldwide**
