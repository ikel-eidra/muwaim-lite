# Android Deployment Guide - MUWAIM Lite v1.1

This guide covers building and deploying MUWAIM to Google Play Store using Expo EAS Build.

## Prerequisites

1. **Google Play Console Account** ($25 one-time fee)
2. **EAS CLI** installed: `npm install -g eas-cli`
3. **Expo Account** (free tier works)
4. **App Signing Key** (managed by Google Play or self-managed)

## Step 1: Configure EAS Project

1. Log in to EAS:
   ```bash
   eas login
   ```

2. Initialize EAS in your project:
   ```bash
   eas init
   ```
   This will create a project ID and update `app.json`.

3. Update `app.json` with your project details:
   ```json
   {
     "expo": {
       "name": "MUWAIM",
       "slug": "muwaim-lite",
       "version": "1.1.0",
       "android": {
         "package": "com.muwaim.lite",
         "versionCode": 1,
         "adaptiveIcon": {
           "foregroundImage": "./assets/images/icon.png",
           "backgroundColor": "#FFFFFF"
         },
         "permissions": [
           "READ_EXTERNAL_STORAGE",
           "WRITE_EXTERNAL_STORAGE"
         ]
       }
     }
   }
   ```

## Step 2: Configure Build Profiles

Your `eas.json` should contain:

```json
{
  "cli": {
    "version": ">= 5.9.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal",
      "android": {
        "buildType": "apk"
      }
    },
    "production": {
      "android": {
        "buildType": "aab"
      }
    }
  },
  "submit": {
    "production": {
      "android": {
        "serviceAccountKeyPath": "./play-store-credentials.json",
        "track": "internal"
      }
    }
  }
}
```

## Step 3: Build for Production

### Option A: Build AAB (Recommended for Play Store)

```bash
eas build --platform android --profile production
```

This will:
- Build an Android App Bundle (`.aab`)
- Upload to EAS servers
- Provide a download link when complete (~15-20 minutes)

### Option B: Build APK (For Testing)

```bash
eas build --platform android --profile preview
```

This creates an installable APK for direct device installation.

## Step 4: Google Play Store Setup

### Create App Listing

1. Go to [Google Play Console](https://play.google.com/console)
2. Click **Create app**
3. Fill in details:
   - **App name:** MUWAIM
   - **Default language:** English (US)
   - **App or game:** App
   - **Free or paid:** Free

### Set Up Store Listing

1. **Product Details:**
   - **App name:** MUWAIM - ATS CV Optimizer
   - **Short description:** Tailor your CV to match job descriptions with AI-powered ATS optimization
   - **Full description:**
     ```
     MUWAIM (موائم) helps jobseekers optimize their CVs for Applicant Tracking Systems (ATS).

     KEY FEATURES (FREE FOREVER):
     ✓ Upload CV (PDF/DOCX)
     ✓ Search jobs or paste job descriptions
     ✓ Instant ATS scoring (0-100)
     ✓ AI-powered CV optimization
     ✓ Generate resume + cover letter
     ✓ Download PDF & DOCX formats
     ✓ Apply via email

     PRO FEATURES (₱99/week or SAR 9.99/week):
     ✓ Unlimited rewrites (10 jobs at once)
     ✓ Bulk cover letters
     ✓ Export all (ZIP + CSV report)
     ✓ Premium templates
     ✓ Ad-free experience

     PRIVACY FIRST:
     🛡️ Data deleted after 24 hours
     🛡️ No data harvesting
     🛡️ AI with no-retain policy

     Languages: English & Arabic (RTL)

     Built for jobless and under-resourced applicants.
     No one should pay just to get hired.
     ```

2. **App icon:** 512x512 PNG (high-res version of your icon)

3. **Feature graphic:** 1024x500 PNG

4. **Screenshots:** Minimum 2, maximum 8 (phone + tablet)
   - Phone: 16:9 or 9:16 aspect ratio
   - Tablet: 16:9 or 9:16 aspect ratio

5. **Categorization:**
   - **App category:** Productivity
   - **Tags:** Jobs, Career, Resume, CV

### Content Rating

1. Complete content rating questionnaire
2. MUWAIM should receive **PEGI 3** or **Everyone** rating
3. No ads targeting children
4. No in-app purchases for minors

### Data Safety

Fill out data safety form (critical for approval):

```
Data Collection:
- ❌ Location: Not collected
- ❌ Personal info: Not collected
- ❌ Financial info: Not collected
- ❌ Photos and videos: Not collected
- ✅ Files and docs: Collected temporarily (CV uploads)
  - Purpose: App functionality
  - Is collection optional? No
  - Is data encrypted in transit? Yes
  - Can users request data deletion? Yes (automatic after 24h)

Data Sharing:
- ❌ No data shared with third parties

Data Retention:
- All uploaded files deleted after 24 hours automatically
- No persistent storage of user data
```

### Pricing & Distribution

1. **Countries:** Select all supported countries
2. **Pricing:** Free (with in-app purchases)
3. **Contains ads:** Yes (free tier only)

## Step 5: Upload Build

### Manual Upload

1. Download the AAB file from EAS build
2. In Play Console, go to **Production > Create new release**
3. Upload the AAB file
4. Add release notes:
   ```
   Version 1.1.0
   - ATS-optimized CV tailoring
   - Job search integration
   - AI-powered optimization
   - Multi-format downloads
   - Arabic & English support
   ```

### Automated Upload (Recommended)

1. Create a service account in Google Cloud Console
2. Download JSON key file as `play-store-credentials.json`
3. Run:
   ```bash
   eas submit --platform android --profile production
   ```

## Step 6: Review & Release

1. **Internal Testing Track:** Upload first, test with team
2. **Closed Testing:** Invite beta testers
3. **Open Testing:** Public beta (optional)
4. **Production:** Submit for review

**Review time:** 1-7 days typically

## Step 7: Post-Launch

### Monitor Metrics

- **Installs:** Google Play Console dashboard
- **Crashes:** Firebase Crashlytics (if configured)
- **Reviews:** Respond to user feedback promptly

### Update Strategy

Version bump:
```json
{
  "version": "1.1.1",
  "android": {
    "versionCode": 2
  }
}
```

Build and submit updates:
```bash
eas build --platform android --profile production
eas submit --platform android --profile production
```

## Troubleshooting

### Build Failures

**Issue:** `JAVA_HOME not set`
**Fix:** EAS handles this automatically, no action needed

**Issue:** `Duplicate permissions`
**Fix:** Remove duplicate entries from `app.json` android.permissions

### Upload Failures

**Issue:** `Version code already exists`
**Fix:** Increment `versionCode` in `app.json`

**Issue:** `Invalid signature`
**Fix:** Use Google Play App Signing (recommended)

### Review Rejections

**Common reasons:**
- Incomplete data safety form → Fill out accurately
- Missing privacy policy → Add link in Play Store listing
- Crashes on startup → Test thoroughly before submission

## Build Commands Reference

```bash
# Development build (for testing)
eas build --platform android --profile development

# Preview APK
eas build --platform android --profile preview

# Production AAB
eas build --platform android --profile production

# Submit to Play Store
eas submit --platform android --profile production

# Check build status
eas build:list

# Download build artifact
eas build:download --platform android
```

## Cost Breakdown

- Google Play Developer Account: $25 (one-time)
- EAS Build Free Tier: 30 builds/month
- EAS Build Paid: $29/month (unlimited builds)
- Hosting (Supabase Free Tier): $0
- Hosting (Supabase Pro): $25/month

## Next Steps

1. Set up crash reporting (Firebase)
2. Configure analytics (Firebase Analytics)
3. Implement in-app purchases (RevenueCat recommended)
4. Add AdMob integration (see ADS_IAP.md)
5. Create promotional assets for Play Store

---

**Ready for Production!** 🚀
