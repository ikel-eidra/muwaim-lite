# Privacy Policy - MUWAIM Lite v1.1

**Effective Date:** October 7, 2025
**Last Updated:** October 7, 2025

## Introduction

MUWAIM ("we," "our," or "the app") is committed to protecting your privacy. This Privacy Policy explains how we handle data in our CV optimization application.

## Our Core Principle

**We do not collect, store, or sell your personal data.**

MUWAIM is built on Trust Architecture v1.0, which means:
- All data is ephemeral (temporary)
- Files are automatically deleted after 24 hours
- No user profiling or tracking
- No data harvesting

## What Data We Process

### Temporarily Processed (Deleted After 24 Hours)

1. **CV/Resume Files**
   - Purpose: Parse and optimize your CV
   - Format: PDF or DOCX
   - Storage: Ephemeral (24-hour auto-delete)
   - Access: Only you via secure download links

2. **Job Descriptions**
   - Purpose: Match against your CV
   - Source: User-provided or public job boards
   - Storage: In-memory processing only
   - Retention: Session-based (cleared on app close)

3. **Generated Documents**
   - Purpose: Provide optimized resumes and cover letters
   - Format: PDF, DOCX, ZIP, CSV
   - Storage: Ephemeral (24-hour auto-delete)
   - Access: Only you via secure download links

### NOT Collected

- ❌ Name, email, or contact information
- ❌ Location data
- ❌ Device identifiers (IMEI, MAC address, etc.)
- ❌ Browsing history
- ❌ Social media profiles
- ❌ Financial information (payment handled by Google/Apple)
- ❌ Biometric data
- ❌ Personal photos or media

## How We Use Data

### CV Optimization Process

1. You upload a CV → Parsed into text
2. You search/paste a job description → Analyzed for keywords
3. We calculate ATS score → Using keyword/structure analysis
4. We generate optimized CV → Using AI (with no-retain policy)
5. You download documents → Files available for 24 hours
6. **Automatic deletion** → All data purged after 24 hours

### AI Processing

We use OpenAI's GPT-4o-mini for:
- CV parsing (extracting text from PDF/DOCX)
- Resume optimization (keyword alignment)
- Cover letter generation

**Important:** All AI API calls include:
- `no-retain` instruction (OpenAI does not store data)
- `no-train` instruction (OpenAI does not use data for training)
- Minimal context (only necessary CV/JD text)

## Data Storage & Security

### Ephemeral Storage

- **Location:** Supabase Cloud (encrypted at rest)
- **Duration:** Maximum 24 hours
- **Purge Schedule:** Automated hourly cleanup
- **Encryption:** TLS 1.3 in transit, AES-256 at rest

### No Persistent Database

MUWAIM does not maintain a user database. There are:
- No user accounts
- No login credentials
- No stored email addresses
- No permanent records

### Logs

We maintain minimal non-PII logs for system health:
- Timestamp
- Function name (e.g., "cv-parse", "ats-score")
- Error codes (if any)
- **NOT logged:** File contents, personal data, CV text

## Third-Party Services

### Supabase (Backend Infrastructure)

- **Purpose:** Host edge functions and ephemeral storage
- **Data shared:** CV text (temporary), job descriptions
- **Retention:** 24 hours maximum
- **Privacy policy:** https://supabase.com/privacy

### OpenAI (AI Processing)

- **Purpose:** CV parsing and optimization
- **Data shared:** CV text, job descriptions (with no-retain flag)
- **Retention:** Not retained (per our API configuration)
- **Privacy policy:** https://openai.com/privacy

### Google Play Services (Android)

- **Purpose:** App distribution, in-app purchases
- **Data shared:** None from MUWAIM (handled by Google)
- **Privacy policy:** https://policies.google.com/privacy

### AdMob (Advertising - Free Tier Only)

- **Purpose:** Show ads to support free tier
- **Data shared:** Device advertising ID (anonymized)
- **Control:** You can disable personalized ads in device settings
- **Privacy policy:** https://support.google.com/admob/answer/6128543

## Your Rights

### Data Access

You have full access to all your data while it exists (up to 24 hours). Download links provide complete copies of:
- Original uploaded CV
- Optimized resume
- Cover letter
- Match report (CSV)

### Data Deletion

Data is automatically deleted after 24 hours. You can also:
- Close the app (session data cleared)
- Uninstall the app (all local data removed)

### Right to Opt-Out

- **Ads:** Disable personalized ads in device settings
- **AI Processing:** Unfortunately, this is core functionality—opting out means you cannot use the app

## Children's Privacy

MUWAIM is not intended for children under 13. We do not knowingly collect data from children.

## International Users

### Data Processing Locations

- **Primary:** United States (Supabase US-West)
- **Backup:** None (ephemeral data is not backed up)

### GDPR Compliance (EU Users)

- **Legal basis:** Consent + Legitimate interest
- **Data controller:** MUWAIM (contact below)
- **Data processor:** Supabase, OpenAI
- **Rights:** Access, deletion (automatic), portability (download links)

### CCPA Compliance (California Users)

We do not "sell" personal information as defined by CCPA.

## Cookies & Tracking

- **Cookies:** None
- **Analytics:** Minimal (only function call counts, no user tracking)
- **Tracking pixels:** None

## Changes to This Policy

We may update this policy. Changes will be posted in-app and at muwaim.app/privacy.

**Version history:**
- v1.0 (October 7, 2025): Initial release

## Contact Us

For privacy questions or concerns:

- **Email:** privacy@muwaim.app
- **Website:** https://muwaim.app/privacy
- **Data Protection Officer:** dpo@muwaim.app

## Trust Architecture Certification

MUWAIM is built on Trust Architecture v1.0:

✅ Ephemeral storage (24h maximum)
✅ No data harvesting or profiling
✅ AI with no-retain/no-train policy
✅ Encrypted in transit and at rest
✅ Open about data practices
✅ User controls (download, automatic deletion)

---

**Last reviewed:** October 7, 2025
**Next review:** January 7, 2026

---

## Data Safety Summary (For Google Play Store)

**Data Collection:**
- Files and documents: YES (CV uploads)
  - Purpose: App functionality
  - Encrypted in transit: YES
  - Encrypted at rest: YES
  - Can users delete data: YES (automatic after 24h)
  - Is collection optional: NO (required for app to work)

**Data Sharing:**
- Third parties: NO (data not shared)

**Data Retention:**
- Maximum: 24 hours
- User control: Automatic deletion

**Security Practices:**
- Data encrypted in transit: YES (TLS 1.3)
- Data encrypted at rest: YES (AES-256)
- Independent security review: Planned Q1 2026
