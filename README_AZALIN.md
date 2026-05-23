# Azalin.io — Panama Relocation Consulting

A high-performance Astro site for Panama relocation and flag theory consulting.

**Live at:** https://azalin.io  
**Design:** Custom dark theme optimized for LLM/SEO discoverability  
**Blog:** Weekly automated posts via Zapier + GitHub

## What's Inside

### Pages

- **Home** (`/`) — Hero, stats, avatar cards, three-tier offers, founder story, blog teaser
- **About** (`/about`) — Founder story (7 countries, 8 years), credentials, Panama FAQ accordion
- **Panama Report** (`/report`) — Lead magnet form (10 fields) → Zapier webhook
- **Blog** (`/blog`) — Weekly insights on Panama, Bitcoin, automation, residency
  - Blog Index: `GET /blog` → lists all posts
  - Blog Post: `GET /blog/[slug]` → full article with JSON-LD schema

### Key Features

#### Design System
- **Colors**: Dark palette (`#0B1826` bg, `#C9A84C` gold accent)
- **Fonts**: Playfair Display (headings) + Inter (body)
- **Components**: Cards, avatars, grids, FAQ accordion, blog cards, pullquotes
- **Responsive**: Mobile-first, hamburger menu at 768px
- **SEO-Ready**: JSON-LD schemas (Article, Organization, FAQ)

#### Lead Magnet Form
Panama Report form with 10 fields:
- `first_name`, `email`, `country`, `income_range`
- `business_structure`, `primary_goal`, `family_situation`
- `bitcoin_exposure`, `biggest_hesitation`, `timeline`
- **Posts to**: Zapier webhook at `https://hooks.zapier.com/hooks/catch/8006666/4o8f19o/`

#### Blog Automation
- Posts stored in `src/data/blog-posts.json`
- Zapier triggers weekly → reads spreadsheet → commits to GitHub → triggers rebuild
- Each post includes: title, slug, excerpt, featured image, category, reading time, markdown content
- Full markdown support: headers, bold/italic, code, tables, links, blockquotes
- Supports featured quotes and CTA buttons

#### Navigation
- Sticky header with logo + nav links (Home, About, Blog, Report)
- Mobile hamburger menu closes on nav click
- Responsive footer with company info, links, legal

### Tech Stack

- **Framework**: Astro 6.3.7 (static generation)
- **Build**: `npm run build` → `dist/` folder
- **Deploy**: Cloudflare Pages (auto-rebuilds on git push)
- **Fonts**: Google Fonts (Playfair Display, Inter)
- **Blog Data**: JSON file + Zapier integration
- **Form**: HTML form → Zapier webhook

## Development

### Local Setup

```bash
cd azalin-site
npm install
npm run dev
# Visit http://localhost:4321
```

### Project Structure

```
src/
├── pages/
│   ├── index.astro          # Home page
│   ├── about.astro          # About + FAQ
│   ├── report.astro         # Lead magnet form
│   └── blog/
│       ├── index.astro      # Blog listing
│       └── [...slug].astro  # Dynamic post pages
├── layouts/
│   └── Base.astro           # Main layout (nav, footer, all CSS)
└── data/
    └── blog-posts.json      # Blog posts (Zapier adds here)
```

### Key Files

| File | Purpose |
|------|---------|
| `src/layouts/Base.astro` | Master layout with design system, nav, footer, JS functions |
| `src/data/blog-posts.json` | Blog post data. Zapier appends new posts here |
| `astro.config.mjs` | Site URL (azalin.io), static output for Cloudflare |
| `DEPLOYMENT_GUIDE.md` | Full deployment to Cloudflare Pages + custom domain |
| `ZAPIER_SETUP.md` | Blog automation workflow configuration |

## Deployment

### One-Time Setup

1. **GitHub**
   ```bash
   git add .
   git commit -m "Initial Astro build"
   git remote add origin https://github.com/YOUR_USER/azalin-site.git
   git push -u origin main
   ```

2. **Cloudflare Pages**
   - Connect GitHub repo
   - Set build command: `npm run build`
   - Set output directory: `dist`
   - Connect custom domain: `azalin.io`

### Continuous Deployment

Every git push → Cloudflare auto-builds & deploys in ~2 minutes.

See `DEPLOYMENT_GUIDE.md` for full setup.

## Zapier Integration

### Blog Post Workflow

Weekly trigger (e.g., Wednesday 6am):
1. Google Sheets → Read this week's row
2. Zapier → Transform to blog post JSON
3. GitHub API → Commit to `src/data/blog-posts.json`
4. Cloudflare Pages → Auto-rebuild
5. Blog post live at `/blog/slug`

### Spreadsheet Columns Required

- `post_title` — Article title
- `post_slug` — URL slug (e.g., `panama-tax-guide-2026`)
- `post_excerpt` — 1-2 sentence summary
- `featured_image` — Full HTTPS URL
- `featured_image_alt` — Alt text
- `author` — Author name
- `category` — One category (Panama, Bitcoin, Automation, etc.)
- `tags` — Comma-separated (panama,taxes,residency)
- `publish_date` — YYYY-MM-DD
- `reading_time` — Integer (minutes)
- `post_body` — **Markdown format**
- `featured_quote` — Optional pullquote text
- `featured_quote_attribution` — Quote attribution
- `meta_description` — SEO meta (160 chars)
- `cta` — Button text (e.g., "Get the Free Report")
- `cta_link` — Button URL

See `ZAPIER_SETUP.md` for detailed Zap configuration.

## SEO & LLM Optimization

### JSON-LD Schemas

- **Organization** (site-wide) — Name, description, contact
- **Article** (each blog post) — Headline, image, author, publish date, URL
- **FAQ** (About page) — All Q&A pairs

### Meta Tags

Every page includes:
- `title` — Page title + "Azalin"
- `description` — SEO summary
- `og:image` — Social sharing image
- `canonical` — Correct URL

### Content Structure

- Proper H1/H2/H3 hierarchy
- Short paragraphs
- Internal links (Home ↔ Blog ↔ Report ↔ About)
- Keywords naturally embedded (Panama, residency, Bitcoin, taxes)

### Targeted for

- ChatGPT, Claude, Gemini (via schema + LLM-friendly content)
- Google Search (structured data, mobile-friendly, fast)
- LinkedIn/Twitter (Open Graph cards)

## Customization

### Colors

Edit CSS variables in `src/layouts/Base.astro`:

```css
:root {
  --bg:      #0B1826;       /* Dark background */
  --bg2:     #112035;       /* Lighter bg */
  --bg3:     #1A2E44;       /* Lighter still */
  --border:  #2A4060;       /* Borders */
  --gold:    #C9A84C;       /* Primary accent */
  --gold-lt: #E4C97A;       /* Light accent */
  --text:    #EDE9E3;       /* Main text */
  --muted:   #7A9BB5;       /* Muted text */
  --radius:  6px;           /* Border radius */
  --max:     1100px;        /* Max container width */
}
```

### Blog Posts

Add to `src/data/blog-posts.json`:

```json
{
  "title": "Your Post Title",
  "slug": "your-post-slug",
  "excerpt": "Short summary for card",
  "featured_image": "https://...",
  "featured_image_alt": "...",
  "author": "Joshua Allen",
  "category": "Panama",
  "tags": ["panama", "..."],
  "publish_date": "2026-05-23",
  "reading_time": 8,
  "body": "# Markdown content here\n\n..."
}
```

### Add New Pages

Create `src/pages/new-page.astro`:

```astro
---
import Base from '../layouts/Base.astro';
---

<Base
  title="Your Page Title"
  description="SEO description"
>
  <!-- Your content -->
</Base>
```

## Performance

- **Lighthouse Score**: Target 90+
- **Bundle Size**: ~50KB gzipped (CSS + HTML)
- **Time to First Byte**: <200ms (Cloudflare edge)
- **Core Web Vitals**: All green (static site, no JS bloat)

## Support

### Docs

- `DEPLOYMENT_GUIDE.md` — Full Cloudflare setup
- `ZAPIER_SETUP.md` — Blog automation workflow
- Inline comments in `src/layouts/Base.astro`

### Common Tasks

**Update home page teaser**  
→ Edit `src/pages/index.astro`, republish content section

**Add new blog post manually**  
→ Add entry to `src/data/blog-posts.json`, commit & push

**Change colors**  
→ Edit `:root` CSS variables in `src/layouts/Base.astro`

**Update FAQ**  
→ Edit `src/pages/about.astro`, FAQ section

## Next Steps

1. ✅ Deploy to Cloudflare Pages
2. ✅ Connect custom domain (azalin.io)
3. ✅ Set up Zapier for weekly blog automation
4. ✅ Test form submission to Zapier
5. ✅ Publish first manual blog post
6. ✅ Monitor Lighthouse scores & Core Web Vitals

---

**Built with Astro 6 + Cloudflare Pages**  
**Updated:** May 2026
