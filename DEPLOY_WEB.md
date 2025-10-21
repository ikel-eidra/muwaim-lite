# Web Deployment Guide - MUWAIM Lite v1.1

This guide covers deploying MUWAIM as a Progressive Web App (PWA) using Expo's web output.

## Overview

MUWAIM can be deployed as a web application alongside the mobile app, providing:
- Desktop access for CV preparation
- Cross-platform compatibility
- SEO benefits for discovery
- Same features as mobile (with platform-specific adaptations)

## Prerequisites

1. **Hosting Platform** (choose one):
   - Vercel (recommended, free tier available)
   - Netlify
   - AWS Amplify
   - Cloudflare Pages

2. **Domain** (optional but recommended):
   - muwaim.app
   - Custom domain via hosting provider

3. **Supabase Project:**
   - Same backend as mobile app
   - Edge Functions deployed
   - Storage bucket configured

## Step 1: Configure for Web

### Update app.json

Ensure web configuration is set:

```json
{
  "expo": {
    "web": {
      "bundler": "metro",
      "output": "server",
      "favicon": "./assets/images/favicon.png"
    },
    "plugins": [
      "expo-router"
    ]
  }
}
```

**Note:** `output: "server"` is required for API routes and server-side rendering.

### Environment Variables

Create `.env.production`:

```env
EXPO_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
```

## Step 2: Build for Production

```bash
# Install dependencies
npm install

# Build web bundle
npm run build:web
```

This generates a `dist/` directory with:
- `_expo/static/` - Static assets
- `server/` - Server-side code (for API routes)
- HTML, CSS, JS files

**Build output location:** `dist/`

## Step 3: Deploy to Vercel (Recommended)

### Option A: Vercel CLI

1. Install Vercel CLI:
   ```bash
   npm install -g vercel
   ```

2. Log in:
   ```bash
   vercel login
   ```

3. Deploy:
   ```bash
   vercel --prod
   ```

### Option B: Vercel Git Integration

1. Push code to GitHub:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin https://github.com/your-username/muwaim-lite.git
   git push -u origin main
   ```

2. Go to [Vercel Dashboard](https://vercel.com/dashboard)

3. Click **Import Project**

4. Select your GitHub repo

5. Configure:
   - **Framework Preset:** Other
   - **Build Command:** `npm run build:web`
   - **Output Directory:** `dist`
   - **Install Command:** `npm install`

6. Add environment variables:
   - `EXPO_PUBLIC_SUPABASE_URL`
   - `EXPO_PUBLIC_SUPABASE_ANON_KEY`

7. Click **Deploy**

**Deployment time:** 2-5 minutes

### Custom Domain

1. Go to **Settings** → **Domains**
2. Add custom domain: `muwaim.app`
3. Configure DNS:
   - Type: CNAME
   - Name: @ (or www)
   - Value: cname.vercel-dns.com
4. Wait for DNS propagation (10-60 minutes)

## Step 4: Deploy to Netlify

### Option A: Netlify CLI

1. Install Netlify CLI:
   ```bash
   npm install -g netlify-cli
   ```

2. Build:
   ```bash
   npm run build:web
   ```

3. Deploy:
   ```bash
   netlify deploy --prod --dir=dist
   ```

### Option B: Netlify Git Integration

1. Push to GitHub (same as Vercel)

2. Go to [Netlify Dashboard](https://app.netlify.com/)

3. Click **Add new site** → **Import an existing project**

4. Select GitHub repo

5. Configure:
   - **Build command:** `npm run build:web`
   - **Publish directory:** `dist`

6. Add environment variables

7. Click **Deploy site**

## Step 5: Platform-Specific Adaptations

### File Upload

Web uses `<input type="file">` instead of `expo-document-picker`:

```typescript
// lib/upload-web.ts
export async function uploadCVWeb(): Promise<File> {
  return new Promise((resolve, reject) => {
    const input = document.createElement('input');
    input.type = 'file';
    input.accept = '.pdf,.docx';

    input.onchange = (e) => {
      const file = (e.target as HTMLInputElement).files?.[0];
      if (file) resolve(file);
      else reject(new Error('No file selected'));
    };

    input.click();
  });
}
```

### Email Composer

Web uses `mailto:` links instead of `expo-mail-composer`:

```typescript
// lib/apply-web.ts
export function applyViaEmail(
  to: string,
  subject: string,
  body: string,
  attachments: string[]
) {
  const mailtoLink = `mailto:${to}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`;
  window.location.href = mailtoLink;

  // For attachments, download files first
  attachments.forEach((url) => {
    const link = document.createElement('a');
    link.href = url;
    link.download = url.split('/').pop() || 'file';
    link.click();
  });
}
```

### Download Files

Web uses direct download links:

```typescript
// lib/download-web.ts
export function downloadFile(url: string, filename: string) {
  const link = document.createElement('a');
  link.href = url;
  link.download = filename;
  link.click();
}
```

## Step 6: SEO Configuration

### Meta Tags

Add to `app/_layout.tsx` (web only):

```typescript
import Head from 'expo-router/head';

export default function RootLayout() {
  return (
    <>
      <Head>
        <title>MUWAIM - ATS CV Optimizer</title>
        <meta name="description" content="Tailor your CV to match job descriptions with AI-powered ATS optimization. Free CV tailoring for jobseekers." />
        <meta name="keywords" content="CV, resume, ATS, job application, cover letter, optimization" />
        <meta property="og:title" content="MUWAIM - ATS CV Optimizer" />
        <meta property="og:description" content="Free AI-powered CV tailoring for jobseekers" />
        <meta property="og:image" content="https://muwaim.app/og-image.png" />
        <meta name="twitter:card" content="summary_large_image" />
      </Head>
      {/* ... rest of layout */}
    </>
  );
}
```

### Sitemap

Create `public/sitemap.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://muwaim.app/</loc>
    <lastmod>2025-10-07</lastmod>
    <priority>1.0</priority>
  </url>
</urlset>
```

### Robots.txt

Create `public/robots.txt`:

```
User-agent: *
Allow: /
Sitemap: https://muwaim.app/sitemap.xml
```

## Step 7: Progressive Web App (PWA)

### Manifest

Create `public/manifest.json`:

```json
{
  "name": "MUWAIM - ATS CV Optimizer",
  "short_name": "MUWAIM",
  "description": "Tailor your CV to match job descriptions",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#2563eb",
  "icons": [
    {
      "src": "/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

### Service Worker

Expo handles this automatically with workbox.

## Step 8: Analytics & Monitoring

### Vercel Analytics

Add to `package.json`:

```bash
npm install @vercel/analytics
```

In `app/_layout.tsx`:

```typescript
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout() {
  return (
    <>
      {/* ... existing layout */}
      <Analytics />
    </>
  );
}
```

### Error Tracking

Use Sentry for production errors:

```bash
npm install @sentry/react-native
```

## Step 9: Performance Optimization

### Image Optimization

Vercel automatically optimizes images. Use:

```typescript
import { Image } from 'expo-image';

<Image
  source={{ uri: 'https://example.com/image.jpg' }}
  style={styles.image}
  contentFit="cover"
/>
```

### Code Splitting

Expo Router automatically code-splits routes.

### Lazy Loading

```typescript
import { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

<Suspense fallback={<Loading />}>
  <HeavyComponent />
</Suspense>
```

## Step 10: Testing

### Local Testing

```bash
# Build and serve locally
npm run build:web
npx serve dist -p 3000
```

Open http://localhost:3000

### Lighthouse Audit

1. Open Chrome DevTools
2. Go to **Lighthouse** tab
3. Run audit
4. Target scores:
   - Performance: 90+
   - Accessibility: 95+
   - Best Practices: 95+
   - SEO: 90+

## Deployment Checklist

- [ ] Build completes without errors
- [ ] Environment variables set correctly
- [ ] Custom domain configured (if using)
- [ ] SSL certificate active (automatic on Vercel/Netlify)
- [ ] PWA manifest included
- [ ] Meta tags for SEO
- [ ] Analytics installed
- [ ] Error tracking configured
- [ ] Tested on multiple browsers (Chrome, Firefox, Safari)
- [ ] Tested on mobile browsers
- [ ] File upload works
- [ ] Download works
- [ ] Email apply works (mailto)

## Browser Compatibility

MUWAIM web supports:
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+
- ⚠️ IE 11 (not supported)

## Cost Breakdown

### Vercel Free Tier
- Bandwidth: 100GB/month
- Build time: 100 hours/month
- Serverless function executions: 100K/month
- **Cost:** $0

### Vercel Pro (If needed)
- Bandwidth: 1TB/month
- Build time: 400 hours/month
- Serverless function executions: 1M/month
- **Cost:** $20/month

### Netlify Free Tier
- Bandwidth: 100GB/month
- Build minutes: 300/month
- **Cost:** $0

## Monitoring

Check deployment status:

```bash
# Vercel
vercel ls

# Netlify
netlify status
```

View logs:

```bash
# Vercel
vercel logs

# Netlify
netlify logs
```

## Troubleshooting

### Build Failures

**Issue:** `Module not found`
**Fix:** Check `tsconfig.json` paths and imports

**Issue:** `Out of memory`
**Fix:** Increase Node memory:
```bash
NODE_OPTIONS=--max_old_space_size=4096 npm run build:web
```

### Runtime Errors

**Issue:** API routes not working
**Fix:** Ensure `output: "server"` in app.json

**Issue:** File upload not working
**Fix:** Use web-specific upload implementation

## Next Steps

1. Set up custom domain
2. Configure CDN (automatic with Vercel/Netlify)
3. Enable preview deployments (automatic)
4. Set up staging environment
5. Configure branch deployments

---

**Web deployment complete!** 🌐
