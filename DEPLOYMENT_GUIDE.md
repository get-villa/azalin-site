# Azalin.io Deployment Guide

Your Astro site is ready to deploy to Cloudflare Pages. Here's the complete setup.

## Local Setup

### 1. Fix Node Dependencies (if needed)

```bash
cd azalin-site
rm -rf node_modules package-lock.json
npm install
```

### 2. Test Locally

```bash
npm run dev
```

Visit `http://localhost:4321` and verify:
- ✅ Home page loads with hero, stats, avatars, blog teaser
- ✅ About page has FAQ accordion (click to expand)
- ✅ Report page form submits to Zapier
- ✅ Navigation works, mobile menu responds
- ✅ Sticky nav, footer present on all pages
- ✅ Blog index page shows posts from src/data/blog-posts.json

### 3. Build for Production

```bash
npm run build
```

This creates a `dist/` folder with your static site.

## GitHub Setup

### 1. Initialize Git (if not done)

```bash
cd azalin-site
git add .
git commit -m "Initial Astro build"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/azalin-site.git
git push -u origin main
```

### 2. Create `.github/workflows/build.yml`

This ensures Cloudflare Pages rebuilds whenever you push:

```yaml
name: Build

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install
      - run: npm run build
      - uses: actions/upload-artifact@v3
        with:
          name: dist
          path: dist/
```

Push this file:
```bash
git add .github/workflows/build.yml
git commit -m "Add GitHub Actions build"
git push
```

## Cloudflare Pages Setup

### 1. Connect GitHub Repository

1. Go to **Cloudflare Dashboard** → **Pages**
2. Click **Create a project**
3. Select **Connect to Git**
4. Authorize GitHub and select `azalin-site` repo
5. Click **Begin setup**

### 2. Configure Build Settings

- **Project name**: `azalin` (or any name)
- **Production branch**: `main`
- **Framework preset**: `Astro`
- **Build command**: `npm run build`
- **Build output directory**: `dist`
- **Root directory**: (leave blank)

### 3. Set Environment Variables

In **Settings** → **Environment Variables**, add (optional for now):
- `NODE_VERSION`: `18`

### 4. Deploy

Click **Save and Deploy**. Cloudflare Pages will:
1. Clone your repo
2. Run `npm install`
3. Run `npm run build`
4. Deploy `dist/` to CDN

Your site is now at: `https://azalin.pages.dev/`

## Connect Custom Domain (azalin.io)

### 1. Add Domain to Cloudflare Pages

In **Pages** → **Your Project** → **Settings** → **Custom Domains**:
- Click **Add custom domain**
- Enter `azalin.io`
- Cloudflare auto-validates if already on Cloudflare

### 2. Configure DNS (if not on Cloudflare)

If `azalin.io` is NOT on Cloudflare yet:
1. In your domain registrar, add a CNAME:
   - **Name**: `azalin` (or `@` for root)
   - **Target**: `azalin.pages.dev`

Wait 24-48h for propagation.

### 3. Verify

```bash
nslookup azalin.io
# Should show Cloudflare's IP
```

Visit `https://azalin.io` — your site is live!

## Zapier Integration (Blog Posts)

### 1. Set Up Blog Posts JSON File

Your `src/data/blog-posts.json` contains posts Zapier will add to.

### 2. Configure Zapier Zap

Follow `ZAPIER_SETUP.md` for complete instructions. Quick overview:

**Trigger**: Google Sheets (weekly)
→ **Action 1**: Filter (only this week's post)
→ **Action 2**: GitHub commit
→ **Action 3**: Cloudflare Pages rebuilds automatically

### 3. Test a Manual Publish

1. Edit `src/data/blog-posts.json`
2. Add a test post object
3. Commit and push:
   ```bash
   git add src/data/blog-posts.json
   git commit -m "Add test blog post"
   git push
   ```
4. Watch Cloudflare Pages deploy (should take ~2 min)
5. Visit `https://azalin.io/blog/your-slug/`

## Environment Setup Checklist

- [ ] GitHub repo created and connected
- [ ] Cloudflare Pages project created
- [ ] Build settings configured (npm run build, dist/)
- [ ] Custom domain connected (azalin.io)
- [ ] Site builds and deploys successfully
- [ ] Blog index page shows at /blog
- [ ] Report form submits to Zapier webhook (test it!)
- [ ] Zapier workflow built and tested
- [ ] First blog post published via Zapier

## Common Issues

### Site won't build

**Error**: "Cannot find module @rollup/rollup-linux-arm64-gnu"

**Fix**: This is a local Node issue. On Cloudflare, we specify Node 18 explicitly:
```yaml
# In .github/workflows/build.yml
node-version: '18'
```

### Blog posts don't appear

**Check**:
1. Post slug matches exactly: `/blog/your-exact-slug/`
2. `publish_date` is today (or before)
3. JSON syntax is valid (use https://jsonlint.com/)
4. Git commit was pushed
5. Cloudflare Pages deployment completed

### Form doesn't submit

**Check**:
1. Zapier webhook URL is correct: `https://hooks.zapier.com/hooks/catch/YOUR_ID/YOUR_TOKEN/`
2. Network tab shows POST request (DevTools → Network)
3. Zapier Zap is enabled and running

### Domain not resolving

**Check**:
1. DNS propagation: `nslookup azalin.io` shows Cloudflare IPs
2. Cloudflare Pages shows custom domain as verified
3. SSL certificate is issued (should be automatic)
4. Try https://azalin.io (not http://)

## Continuous Deployment

Every time you:
1. **Push to GitHub** → Cloudflare Pages rebuilds automatically
2. **Zapier commits** → Same rebuild process
3. **Takes ~2 minutes** from push to live

This is fully automated. No manual deploys needed!

## Site Structure Reference

```
azalin-site/
├── src/
│   ├── pages/
│   │   ├── index.astro          # Home page
│   │   ├── about.astro          # About page
│   │   ├── report.astro         # Panama Report form
│   │   └── blog/
│   │       ├── index.astro      # Blog index
│   │       └── [...slug].astro  # Dynamic blog post route
│   ├── layouts/
│   │   └── Base.astro           # Main layout (nav, footer, CSS)
│   ├── data/
│   │   └── blog-posts.json      # Blog posts data (Zapier adds here)
│   └── styles/
│       └── global.css           # (if needed)
├── public/                       # Static assets
├── dist/                         # Build output (generated)
├── astro.config.mjs             # Astro config (updated for azalin.io)
├── ZAPIER_SETUP.md              # Zapier integration guide
└── DEPLOYMENT_GUIDE.md          # This file
```

## Useful Commands

```bash
# Local development
npm run dev

# Build for production
npm run build

# Deploy to Cloudflare (automatic, just git push)
git add .
git commit -m "Update"
git push origin main

# View Cloudflare Pages deployment logs
# → Cloudflare Dashboard → Pages → azalin → Deployments
```

---

**You're ready to deploy!** 🚀

Next steps:
1. Run `npm run build` locally to verify
2. Push to GitHub
3. Connect to Cloudflare Pages
4. Set up Zapier for weekly blog automation
5. Visit https://azalin.io
