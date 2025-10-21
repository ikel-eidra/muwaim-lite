# AdMob & In-App Purchases Setup - MUWAIM Lite v1.1

This guide covers monetization setup for MUWAIM, including AdMob (ads) and RevenueCat (subscriptions).

## Overview

MUWAIM uses a freemium model:
- **Free Tier:** Supported by AdMob ads
- **Pro Tier:** ₱99/week or SAR 9.99/week (removes ads + premium features)

## Part 1: AdMob Integration

### Prerequisites

1. **Google AdMob Account:** https://admob.google.com
2. **Expo Project:** Must use native build (EAS Build)

### Step 1: Create AdMob App

1. Go to [AdMob Console](https://apps.admob.google.com)
2. Click **Apps** → **Add App**
3. Select **Android** (repeat for iOS later)
4. Choose **Yes, my app is listed on a supported app store**
5. Search for "MUWAIM" (or enter package name: `com.muwaim.lite`)
6. Click **Continue**

**Copy your App ID:** `ca-app-pub-XXXXXXXXXXXXXXXX~XXXXXXXXXX`

### Step 2: Create Ad Units

Create 3 ad units:

#### 1. Banner Ad (Footer)
- **Format:** Banner
- **Name:** MUWAIM Footer Banner
- **ID:** `ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX`

#### 2. Interstitial Ad (After CV Upload)
- **Format:** Interstitial
- **Name:** MUWAIM CV Upload Interstitial
- **ID:** `ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX`

#### 3. Rewarded Ad (Download Boost)
- **Format:** Rewarded
- **Name:** MUWAIM Download Reward
- **ID:** `ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX`

### Step 3: Configure App

**IMPORTANT:** AdMob requires native code, so you must:

1. Export your Expo project:
   ```bash
   expo prebuild
   ```

2. Install AdMob module:
   ```bash
   npx expo install expo-ads-admob
   ```

3. Update `app.json`:
   ```json
   {
     "expo": {
       "plugins": [
         [
           "expo-ads-admob",
           {
             "androidAppId": "ca-app-pub-XXXXXXXXXXXXXXXX~XXXXXXXXXX",
             "iosAppId": "ca-app-pub-XXXXXXXXXXXXXXXX~XXXXXXXXXX"
           }
         ]
       ]
     }
   }
   ```

### Step 4: Implement Ads in Code

Create `lib/ads.ts`:

```typescript
import { Platform } from 'react-native';
import {
  AdMobBanner,
  AdMobInterstitial,
  AdMobRewarded,
  setTestDeviceIDAsync,
} from 'expo-ads-admob';

// TEST IDs (replace with real IDs in production)
const BANNER_ID = __DEV__
  ? Platform.select({
      ios: 'ca-app-pub-3940256099942544/2934735716',
      android: 'ca-app-pub-3940256099942544/6300978111',
    })
  : Platform.select({
      ios: 'ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX', // TODO: Replace
      android: 'ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX', // TODO: Replace
    });

const INTERSTITIAL_ID = __DEV__
  ? Platform.select({
      ios: 'ca-app-pub-3940256099942544/4411468910',
      android: 'ca-app-pub-3940256099942544/1033173712',
    })
  : Platform.select({
      ios: 'ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX', // TODO: Replace
      android: 'ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX', // TODO: Replace
    });

const REWARDED_ID = __DEV__
  ? Platform.select({
      ios: 'ca-app-pub-3940256099942544/1712485313',
      android: 'ca-app-pub-3940256099942544/5224354917',
    })
  : Platform.select({
      ios: 'ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX', // TODO: Replace
      android: 'ca-app-pub-XXXXXXXXXXXXXXXX/XXXXXXXXXX', // TODO: Replace
    });

let interstitialShownCount = 0;

export async function initAds() {
  if (__DEV__) {
    await setTestDeviceIDAsync('EMULATOR');
  }
}

export function showInterstitialAd() {
  // Limit: 1 interstitial per 3 sessions
  interstitialShownCount++;
  if (interstitialShownCount % 3 !== 0) return;

  AdMobInterstitial.setAdUnitID(INTERSTITIAL_ID);
  AdMobInterstitial.requestAdAsync({ servePersonalizedAds: true })
    .then(() => AdMobInterstitial.showAdAsync())
    .catch((error) => console.log('Interstitial error:', error));
}

export function showRewardedAd(onRewarded: () => void) {
  AdMobRewarded.setAdUnitID(REWARDED_ID);

  AdMobRewarded.addEventListener('rewardedVideoUserDidEarnReward', () => {
    onRewarded();
  });

  AdMobRewarded.requestAdAsync({ servePersonalizedAds: true })
    .then(() => AdMobRewarded.showAdAsync())
    .catch((error) => console.log('Rewarded ad error:', error));
}

export { BANNER_ID };
```

### Step 5: Add Banner to Results Screen

In `app/(tabs)/matches.tsx`:

```typescript
import { AdMobBanner } from 'expo-ads-admob';
import { BANNER_ID } from '@/lib/ads';
import { useStore } from '@/lib/store';

export default function MatchesScreen() {
  const isPro = useStore((state) => state.isPro);

  return (
    <ScrollView>
      {/* Your existing code */}

      {!isPro && (
        <View style={styles.adContainer}>
          <AdMobBanner
            bannerSize="smartBanner"
            adUnitID={BANNER_ID}
            servePersonalizedAds={true}
            onDidFailToReceiveAdWithError={(e) => console.log('Ad error:', e)}
          />
        </View>
      )}
    </ScrollView>
  );
}
```

### Step 6: Trigger Interstitial After CV Upload

In `app/(tabs)/index.tsx`:

```typescript
import { showInterstitialAd } from '@/lib/ads';

const handleUploadCV = async () => {
  // ... existing upload logic ...

  if (!isPro) {
    showInterstitialAd();
  }
};
```

## Part 2: In-App Purchases (IAP) Setup

### Why RevenueCat?

RevenueCat handles:
- Cross-platform subscriptions (iOS + Android)
- Receipt validation
- Subscription status sync
- Analytics
- Webhooks

**Alternative:** Google Play Billing Library (Android-only)

### Step 1: Create RevenueCat Account

1. Go to [RevenueCat](https://www.revenuecat.com/)
2. Sign up (free tier: 10K MTR)
3. Create a new project: "MUWAIM"

### Step 2: Configure Platform

#### Android Configuration

1. Go to **Project Settings** → **Google**
2. Upload `play-store-credentials.json` (from Play Console)
3. Enter package name: `com.muwaim.lite`

#### iOS Configuration (Later)

1. Go to **Project Settings** → **Apple**
2. Upload App Store Connect API Key
3. Enter bundle ID: `com.muwaim.lite`

### Step 3: Create Products

1. Go to **Products** → **New**
2. Create product:
   - **Identifier:** `pro_weekly`
   - **Type:** Subscription
   - **Duration:** Weekly
   - **Price:** ₱99 / SAR 9.99

3. Map to store SKUs:
   - **Android SKU:** `pro_weekly_php99` (create in Play Console)
   - **iOS SKU:** `pro_weekly_php99` (create in App Store Connect)

### Step 4: Create Entitlement

1. Go to **Entitlements** → **New**
2. Name: `pro`
3. Attach product: `pro_weekly`

### Step 5: Install RevenueCat SDK

**CRITICAL:** RevenueCat requires native code. Export your Expo project:

```bash
expo prebuild
```

Install SDK:

```bash
npm install react-native-purchases
```

Configure `app.json`:

```json
{
  "expo": {
    "plugins": [
      [
        "react-native-purchases",
        {
          "androidApiKey": "YOUR_ANDROID_API_KEY"
        }
      ]
    ]
  }
}
```

### Step 6: Implement IAP

Create `lib/iap.ts`:

```typescript
import Purchases, { PurchasesOffering } from 'react-native-purchases';

const REVENUECAT_API_KEY = {
  ios: 'YOUR_IOS_API_KEY',
  android: 'YOUR_ANDROID_API_KEY',
};

export async function initIAP() {
  if (Platform.OS === 'ios') {
    await Purchases.configure({ apiKey: REVENUECAT_API_KEY.ios });
  } else {
    await Purchases.configure({ apiKey: REVENUECAT_API_KEY.android });
  }
}

export async function getOfferings(): Promise<PurchasesOffering | null> {
  try {
    const offerings = await Purchases.getOfferings();
    return offerings.current;
  } catch (error) {
    console.error('Get offerings error:', error);
    return null;
  }
}

export async function purchasePro(): Promise<boolean> {
  try {
    const offering = await getOfferings();
    if (!offering) return false;

    const weeklyPackage = offering.weekly;
    if (!weeklyPackage) return false;

    const { customerInfo } = await Purchases.purchasePackage(weeklyPackage);
    return customerInfo.entitlements.active['pro'] !== undefined;
  } catch (error) {
    console.error('Purchase error:', error);
    return false;
  }
}

export async function restorePurchases(): Promise<boolean> {
  try {
    const customerInfo = await Purchases.restorePurchases();
    return customerInfo.entitlements.active['pro'] !== undefined;
  } catch (error) {
    console.error('Restore error:', error);
    return false;
  }
}

export async function checkProStatus(): Promise<boolean> {
  try {
    const customerInfo = await Purchases.getCustomerInfo();
    return customerInfo.entitlements.active['pro'] !== undefined;
  } catch (error) {
    console.error('Check status error:', error);
    return false;
  }
}
```

### Step 7: Update Settings Screen

In `app/(tabs)/settings.tsx`:

```typescript
import { purchasePro, restorePurchases, checkProStatus } from '@/lib/iap';

export default function SettingsScreen() {
  const { isPro, setIsPro } = useStore();

  useEffect(() => {
    checkProStatus().then(setIsPro);
  }, []);

  const handleUpgradePro = async () => {
    const success = await purchasePro();
    if (success) {
      setIsPro(true);
      Alert.alert('Success', 'Welcome to MUWAIM Pro!');
    } else {
      Alert.alert('Error', 'Purchase failed. Please try again.');
    }
  };

  const handleRestorePurchases = async () => {
    const success = await restorePurchases();
    if (success) {
      setIsPro(true);
      Alert.alert('Success', 'Purchases restored!');
    } else {
      Alert.alert('Info', 'No active subscriptions found.');
    }
  };

  // ... rest of component
}
```

## Part 3: Play Store Product Setup

### Create Subscription

1. Go to **Play Console** → **Monetize** → **Subscriptions**
2. Click **Create subscription**
3. Fill in:
   - **Product ID:** `pro_weekly_php99`
   - **Name:** MUWAIM Pro Weekly
   - **Description:** Unlimited CV rewrites, no ads, premium features
   - **Billing period:** Weekly
   - **Price:** ₱99.00 (PHP) / SAR 9.99 (SAR)
   - **Free trial:** 7 days (optional)
   - **Grace period:** 3 days (recommended)

4. Add more pricing tiers:
   - Philippines: ₱99/week
   - Saudi Arabia: SAR 9.99/week
   - UAE: AED 9.99/week
   - USA: $2.99/week

5. **Save** and **Activate**

## Part 4: Testing

### Test AdMob

```bash
# Build with test ads
eas build --platform android --profile preview

# Install on device
# Ads will show test banners/interstitials
```

### Test IAP

1. Add test accounts in Play Console:
   - **Play Console** → **Settings** → **License testing**
   - Add email addresses

2. Build and install test version:
   ```bash
   eas build --platform android --profile preview
   ```

3. Test purchase flow (no real charge)

## Part 5: Revenue Split

As per specifications:
- **85%** net revenue to developer (after store fees)
- **15%** to MUWAIM Free-Tier maintenance fund

Google Play takes ~15%, so:
- User pays: ₱99
- Google keeps: ~₱15 (15%)
- Net: ₱84
- Developer: ₱71.40 (85%)
- Free-Tier fund: ₱12.60 (15%)

## Part 6: Analytics

Track key metrics:

```typescript
// In lib/analytics.ts
export function trackAdImpression(adType: string) {
  // Firebase Analytics or RevenueCat
  console.log('Ad shown:', adType);
}

export function trackProUpgrade() {
  console.log('Pro upgrade successful');
}

export function trackAdRevenue(value: number) {
  console.log('Ad revenue:', value);
}
```

## Troubleshooting

### AdMob Issues

**Problem:** Ads not showing
**Fix:**
- Check app is approved in AdMob
- Wait 24-48 hours after approval
- Verify ad unit IDs are correct

**Problem:** "App not configured"
**Fix:** Run `expo prebuild` and rebuild

### IAP Issues

**Problem:** "Product not found"
**Fix:**
- Ensure subscription is active in Play Console
- Check product ID matches exactly
- Wait 24 hours after creating product

**Problem:** "Receipt validation failed"
**Fix:** Check RevenueCat API key is correct

## TODO Before Launch

- [ ] Replace all test ad unit IDs with production IDs
- [ ] Set up RevenueCat production API keys
- [ ] Create Play Store subscription products
- [ ] Test real purchase flow (with test account)
- [ ] Configure Firebase Analytics (optional)
- [ ] Set up webhook for subscription events (RevenueCat → Backend)
- [ ] Create promotional offers (optional)
- [ ] Enable introductory pricing (7-day trial)

## Build Command with Ads & IAP

```bash
# Build production version
eas build --platform android --profile production

# Ensure app.json includes:
# - expo-ads-admob plugin
# - react-native-purchases plugin
# - Correct ad unit IDs
# - RevenueCat API keys
```

---

**Monetization ready!** 💰
