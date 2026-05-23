# Zapier Blog Automation Setup

This guide shows how to set up Zapier to automatically publish blog posts to your Astro site.

## Architecture

Since your Astro site is **static** (output: static), we use this flow:

1. **Zapier** reads your blog post spreadsheet weekly
2. **Zapier** transforms the data into a blog post object
3. **Zapier** commits the new post to GitHub
4. **Cloudflare Pages** automatically rebuilds and deploys

## Spreadsheet Structure

Your spreadsheet should have these columns (one per row, with each column being a week's post):

| Column | Value | Notes |
|--------|-------|-------|
| `post_title` | string | Title of the post |
| `post_slug` | string | URL-safe slug (e.g., `panama-high-earners-2026`) |
| `post_excerpt` | string | 1-2 sentence summary for card/SEO |
| `featured_image` | URL | Full image URL (cloudinary, unsplash, etc.) |
| `featured_image_alt` | string | Alt text for accessibility |
| `author` | string | Author name (default: "Joshua Allen") |
| `category` | string | One category: Panama, Bitcoin, Automation, Residency, etc. |
| `tags` | string | Comma-separated: panama,taxes,residency |
| `publish_date` | date | YYYY-MM-DD format |
| `reading_time` | number | Estimated minutes |
| `post_body` | text | **Markdown format** (see below) |
| `featured_quote` | text | Optional pullquote |
| `featured_quote_attribution` | string | Who said it |
| `meta_description` | string | SEO meta description (160 chars max) |
| `cta` | string | Optional CTA button text |
| `cta_link` | URL | Where CTA button links to |

## Markdown Format for post_body

Use **standard Markdown**:

```markdown
## Section Heading

This is a paragraph with **bold** and *italic* text.

### Subsection

- Bullet point
- Another point

| Column A | Column B |
|----------|----------|
| Cell 1   | Cell 2   |

[Link text](https://example.com)

> Blockquote

`inline code`

\`\`\`
code block
\`\`\`
```

## Zapier Zap Configuration

### Trigger: Schedule (Weekly)

Set to run **every Wednesday at 6am UTC** (or your preferred time).

### Step 1: Google Sheets - Get Spreadsheet Values

- **Spreadsheet**: Your blog post spreadsheet
- **Worksheet**: "Posts" (or whatever you call it)
- **Get Range**: A1:N1 (adjust for your column count)

This returns all columns for that week's row.

### Step 2: Filter - Only if post is for this week

Add a condition:
- **If** `publish_date` **is exactly** `today's date`

This ensures only this week's post publishes.

### Step 3: Create JSON Object

Use a **Code by Zapier** step to transform the spreadsheet row into our blog post format:

```javascript
// Input: output from Google Sheets
const sheetData = inputData;

// Calculate reading time if not provided
const reading_time = parseInt(sheetData.reading_time) || 
  Math.ceil(sheetData.post_body.split(' ').length / 200);

// Parse featured_quote if it exists (format: "quote text" — attribution)
let featured_quote = null;
if (sheetData.featured_quote) {
  const parts = sheetData.featured_quote.split('—');
  featured_quote = {
    text: parts[0].trim().replace(/^["']|["']$/g, ''),
    attribution: parts[1] ? parts[1].trim() : ''
  };
}

return {
  title: sheetData.post_title,
  slug: sheetData.post_slug,
  excerpt: sheetData.post_excerpt,
  featured_image: sheetData.featured_image,
  featured_image_alt: sheetData.featured_image_alt,
  author: sheetData.author || 'Joshua Allen',
  category: sheetData.category,
  tags: sheetData.tags.split(',').map(t => t.trim()),
  publish_date: sheetData.publish_date,
  reading_time: reading_time,
  body: sheetData.post_body,
  featured_quote: featured_quote,
  cta: sheetData.cta || 'Get the Free Panama Report',
  cta_link: sheetData.cta_link || '/report/',
  meta_description: sheetData.meta_description
};
```

### Step 4: GitHub - Create File

Use Zapier's GitHub integration to commit the new post:

- **Repo**: joshallen647/azalin-site (or your repo)
- **Path**: `src/data/blog-posts.json`
- **Action**: Update File
- **File Content**: 

Use a **Code by Zapier** step to read the current blog-posts.json, append the new post, and return the updated JSON:

```javascript
// This is complex - see Alternative Approach below
```

## Alternative (Easier) Approach: Webhook + GitHub Actions

Instead of Zapier writing directly, use a simpler flow:

1. **Zapier** POSTs to a Cloudflare Worker (or Zapier webhook)
2. **Cloudflare Worker** commits to GitHub
3. **GitHub Actions** validates the post
4. **Cloudflare Pages** rebuilds

### Setup Cloudflare Worker

Create a Cloudflare Worker with this code:

```javascript
export default {
  async fetch(request) {
    if (request.method !== 'POST') {
      return new Response('Method not allowed', { status: 405 });
    }

    const post = await request.json();
    
    // Read current blog posts
    const response = await fetch('https://raw.githubusercontent.com/YOUR_USER/azalin-site/main/src/data/blog-posts.json');
    const posts = await response.json();
    
    // Add new post
    posts.unshift(post);
    
    // Commit to GitHub
    const commitMessage = `Add blog post: ${post.title}`;
    const fileContent = Buffer.from(JSON.stringify(posts, null, 2)).toString('base64');
    
    await fetch('https://api.github.com/repos/YOUR_USER/azalin-site/contents/src/data/blog-posts.json', {
      method: 'PUT',
      headers: {
        'Authorization': `token ${GITHUB_TOKEN}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        message: commitMessage,
        content: fileContent,
        branch: 'main'
      })
    });
    
    return new Response('Post published!');
  }
};
```

Then have Zapier POST to your Worker URL.

## After Publishing

Once Zapier commits the post:

1. **Cloudflare Pages** detects the GitHub push
2. **Runs** `npm run build`
3. **Deploys** the new blog post to `/blog/slug-here`

The post is live in ~2 minutes.

## Testing

Before going live, test with:

1. Create a test spreadsheet row
2. Run the Zap manually
3. Check that your GitHub repo has the new post in `src/data/blog-posts.json`
4. Verify Cloudflare Pages deployment completes
5. Visit `https://azalin.io/blog/your-slug/`

## Troubleshooting

**Post not appearing:**
- Check GitHub has the file at `src/data/blog-posts.json`
- Verify the slug matches exactly
- Check Cloudflare Pages deployment logs

**Formatting broken:**
- Ensure post_body is valid Markdown
- Test Markdown parsing locally

**Images not loading:**
- Use full HTTPS URLs for featured_image
- Test the image URL in a browser
