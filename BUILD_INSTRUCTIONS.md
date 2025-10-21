# MUWAIM Lite v1.1 - Build Instructions

Complete guide to build and deploy MUWAIM from source.

## Quick Start

```bash
# 1. Install dependencies
npm install

# 2. Configure environment
cp .env.example .env
# Edit .env with your Supabase credentials

# 3. Start development server
npm run dev

# 4. Build for production
npm run build:android  # Android AAB
npm run build:web      # Web bundle
```

## Prerequisites

### Required Software
- Node.js 18+ (https://nodejs.org/)
- npm 8+ or yarn 1.22+
- Git
- Expo CLI: `npm install -g expo-cli`
- EAS CLI: `npm install -g eas-cli`

### Required Accounts
- Supabase account (free tier)
- OpenAI API account (for GPT-4o-mini)
- Google Play Developer account ($25 one-time)
- EAS Build account (free tier: 30 builds/month)

### Optional Accounts
- AdMob account (for monetization)
- RevenueCat account (for IAP)
- Firebase account (for analytics/crashlytics)

## Environment Setup

### 1. Clone Repository

```bash
git clone https://github.com/your-org/muwaim-lite.git
cd muwaim-lite
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment Variables

Create `.env` file:

```env
# Supabase Configuration
EXPO_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# For Edge Functions (set in Supabase dashboard)
# OPENAI_API_KEY=sk-...
# SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Supabase Backend Setup

### 1. Create Supabase Project

1. Go to https://supabase.com/dashboard
2. Click "New project"
3. Name: MUWAIM
4. Database password: (generate strong password)
5. Region: Choose closest to target users
6. Wait for provisioning (~2 minutes)

### 2. Set Up Storage

Run the storage purge SQL:

```bash
# In Supabase SQL Editor
# Paste contents of supabase/storage-purge.sql
```

Or via CLI:

```bash
supabase db push --file supabase/storage-purge.sql
```

### 3. Deploy Edge Functions

```bash
# Deploy all functions at once
supabase functions deploy cv-parse
supabase functions deploy jobs-search
supabase functions deploy ats-score
supabase functions deploy ats-align
supabase functions deploy cover-letter
supabase functions deploy doc-render
```

Or deploy individually:

```bash
cd supabase/functions/cv-parse
supabase functions deploy cv-parse
```

### 4. Set Function Secrets

In Supabase Dashboard → Settings → Edge Functions → Secrets:

```bash
OPENAI_API_KEY=sk-...
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Mobile App Build

### Development Build (Testing)

```bash
# iOS
eas build --platform ios --profile development

# Android
eas build --platform android --profile development
```

Install on device via QR code or download link.

### Preview Build (APK for Testing)

```bash
eas build --platform android --profile preview
```

Download APK and install on Android device.

### Production Build (Play Store)

```bash
# Build AAB
eas build --platform android --profile production

# Submit to Play Store
eas submit --platform android --profile production
```

**Build time:** 15-25 minutes

## Web Build

### Development

```bash
npm run dev
# Opens Expo DevTools in browser
# Press 'w' to open in web browser
```

### Production Build

```bash
npm run build:web
```

**Output:** `dist/` directory

**Deploy:**

```bash
# Vercel
vercel --prod

# Netlify
netlify deploy --prod --dir=dist

# Manual upload to any static host
# Upload contents of dist/ folder
```

## Testing

### Unit Tests (TODO)

```bash
npm run test
```

### Type Checking

```bash
npm run typecheck
```

### Linting

```bash
npm run lint
```

### Manual Testing Checklist

- [ ] Upload PDF CV → Text extracted correctly
- [ ] Upload DOCX CV → Text extracted correctly
- [ ] Search jobs with keyword → 10 results returned
- [ ] Calculate ATS score → Score 0-100 displayed
- [ ] Select job → Tailor screen opens
- [ ] Apply changes → Optimized CV generated
- [ ] Download PDF → File downloads successfully
- [ ] Download DOCX → File downloads successfully
- [ ] Download ZIP → All files included
- [ ] Apply via email → Email composer opens (mobile) or mailto works (web)
- [ ] Switch language EN→AR → UI mirrors (RTL)
- [ ] Switch language AR→EN → UI mirrors (LTR)
- [ ] Pro upgrade prompt → Displays correctly
- [ ] Ads display (if not Pro) → Banner/interstitial shows

## Platform-Specific Notes

### Android

**Minimum SDK:** 21 (Android 5.0)
**Target SDK:** 34 (Android 14)

**Permissions required:**
- READ_EXTERNAL_STORAGE (for CV upload)
- WRITE_EXTERNAL_STORAGE (for downloads)
- INTERNET (for API calls)

**Build output:**
- Development: APK (~50MB)
- Production: AAB (~25MB)

### Web

**Supported browsers:**
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

**Bundle size:** ~2-3MB gzipped

## Troubleshooting

### Build Errors

**Error:** `Unable to resolve module`
**Fix:**
```bash
rm -rf node_modules
npm install
```

**Error:** `Expo config is invalid`
**Fix:** Check `app.json` syntax

**Error:** `EAS Build failed: Out of memory`
**Fix:** Use EAS Build paid tier or reduce bundle size

### Runtime Errors

**Error:** `Network request failed`
**Fix:** Check Supabase URL and API keys

**Error:** `Function not found`
**Fix:** Ensure Edge Functions are deployed

**Error:** `Storage bucket not found`
**Fix:** Run storage-purge.sql

### Deployment Errors

**Error:** `App not found in Play Console`
**Fix:** Create app in Play Console first

**Error:** `Invalid AAB`
**Fix:** Rebuild with `eas build --platform android --profile production`

## Build Artifacts

After successful build, you'll have:

```
builds/
├── android/
│   ├── app-production.aab        # Play Store upload
│   ├── app-preview.apk           # Direct install
│   └── mapping.txt               # ProGuard mapping
├── web/
│   └── dist/                     # Web bundle
│       ├── _expo/
│       ├── server/
│       └── index.html
└── ios/ (future)
    └── app.ipa
```

## Environment Variables Reference

### Mobile App (.env)

```env
EXPO_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=eyJ...
```

### Edge Functions (Supabase Dashboard)

```env
OPENAI_API_KEY=sk-...
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_ROLE_KEY=eyJ...
```

### Web Deployment (Vercel/Netlify)

```env
EXPO_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=eyJ...
```

## Continuous Integration (CI/CD)

### GitHub Actions Example

Create `.github/workflows/build.yml`:

```yaml
name: Build MUWAIM

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Type check
        run: npm run typecheck

      - name: Lint
        run: npm run lint

      - name: Build web
        run: npm run build:web

      - name: EAS Build
        if: github.ref == 'refs/heads/main'
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
        run: |
          npm install -g eas-cli
          eas build --platform android --profile production --non-interactive
```

## Build Time Estimates

- **Development build:** 15-20 minutes
- **Preview build (APK):** 10-15 minutes
- **Production build (AAB):** 15-25 minutes
- **Web build:** 2-5 minutes
- **Edge Functions deploy:** 1-2 minutes each

## Resource Requirements

### Development Machine

- **CPU:** 4+ cores recommended
- **RAM:** 8GB minimum, 16GB recommended
- **Disk:** 10GB free space
- **Internet:** Stable connection (uploads ~50MB builds)

### EAS Build

- Free tier: 30 builds/month
- Paid tier: $29/month unlimited builds
- Build queue: Usually < 5 minutes wait

## Support

For build issues:
- **Expo Forums:** https://forums.expo.dev
- **Expo Discord:** https://chat.expo.dev
- **Supabase Discord:** https://discord.supabase.com
- **GitHub Issues:** https://github.com/your-org/muwaim-lite/issues

## License

Proprietary - All rights reserved

---

**Happy Building!** 🚀
