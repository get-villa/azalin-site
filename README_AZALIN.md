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

## Session Log

### 2026-05-31 — Canadian-Only Pivot (Phase 1)

**Scope:** Removed all UK/US copy across index.astro, about.astro, report.astro. webinars.astro was clean — no changes needed.

**Changes made:**
- **index.astro**: Updated meta description; rewrote hero lead to hit "accountant ceiling" pain point; removed 🇬🇧/🇺🇸 country pills; updated stat to "47–53%" with Canadian-specific label; replaced James Whitfield (UK) with Michael Reeves (Vancouver agency owner, research paralysis quote); replaced Jennifer Harlow (USA) with Sarah MacLeod (Toronto consultant, "fifty-three cents to CRA" quote); inserted new **Three Paths section** (Snowbird / Second Residency / Full Relocation with Article 694 citation); renamed The Panama Retreat → **The Panama Executive Programme** (7-day, remote ops + Bitcoin + wellness, corporate deductibility language, FNV removed).
- **about.astro**: Replaced single Canadian card (simple) with expanded card explaining all three taxation systems (residency-based/Canada, citizenship-based/USA only, territorial/Panama); removed British Nationals and Americans cards entirely; updated CRS FAQ title and answer to Canada-only; updated cost of living to Vancouver/Toronto + western standards paragraph (JCI hospitals, English, USD); added two new FAQ items: "My accountant says I'm already doing the main things" and "Is this only for billionaires?" (Article 694 citation).
- **report.astro**: Updated hero lead to Canadians-only; updated territorial tax check item to Canadian-specific framing; replaced country-specific bullet with Three Paths bullet; updated dropdown to Canada / US (coming soon) / UK (coming soon); replaced three-card "Tailored to Your Country" section with single expanded Canadian card.

**Note:** The British professional case study in about.astro (lines ~82–83) was intentionally kept — it pre-dates formal services and illustrates the consulting origin story.

---

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

---

## Session Log — 2026-06-07

**Completed:**
- Homepage hero rewrite ("You Built Something Real. Canada Is Taking Half of It Every Year.")
- Stats updated ($150K+ anchor, 50+ FNV nations)
- David Chen quote updated (irony/diversification framing)
- Country pill → "Founders · Owners · Executives"
- Opportunity cost section added (S&P 500 2015–2024, $1M → $2.27M)
- Founder section moved above Offers
- Offers reordered: Report → Scouting Trip → Bitcoin → Executive Programme
- Offer labels: FREE / DONE-BESIDE-YOU / $197 · ONLINE / BY APPLICATION
- Portrait OG image created (1080×1920, queen piece) for iMessage tall card
- Mobile offer form stacking fixed (waitlist forms now full-width vertical on mobile)
- Footer copy updated: "For Canadians sending $100,000 or more to CRA every year..."
- OG image 403 investigated — was sandbox proxy issue, not real. LinkedIn preview confirmed working.

**Decisions Made:**
- Lifestyle Design Strategy approved for site integration
- Calculator feature greenlit (interactive, no email gate, silent Zapier capture)
- /the-model/ page planned
- "What the Other Side Looks Like" homepage section planned

**Pending / Waiting on Joshua:**
- Answer 4 open questions in Lifestyle_Design_Calculator.md before build can start
- Webinar registration page (not yet started)

**⚠️ DO NOT FORGET — CALENDAR VISUAL:**
Build a before/after weekly calendar visual (CSS grid or SVG):
- LEFT: Canadian owner week — Mon–Fri fully blocked, 8am–6pm, grey
- RIGHT: Panama model week — Tue/Wed/Thu, 3-hour gold deep work blocks, rest open
Needed in BOTH: homepage "What the Other Side Looks Like" section AND lifestyle calculator output panel.

**Next Recommended Actions (priority order):**
1. Joshua answers 4 open questions in Lifestyle_Design_Calculator.md
2. Build "What the Other Side Looks Like" homepage section
3. Build webinar registration page + Zapier automation
4. Build /lifestyle/ calculator page once Zapier webhook is ready
5. Build /the-model/ philosophy page
