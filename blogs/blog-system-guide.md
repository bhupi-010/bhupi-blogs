# Dynamic Blog System Guide (Next.js + GitHub)

This guide explains how your dynamic blog system works using Next.js (App Router) and GitHub as a headless CMS.

## 🚀 Architecture Overview
- **Content Store**: A separate public GitHub repo (`bhupi-blogs`).
- **Data Fetching**: Fetched via GitHub's REST API and Raw content URLs.
- **Rendering**: Markdown is parsed with `gray-matter` and rendered to HTML via `remark`.
- **Performance**: High-speed with **ISR (Incremental Static Regeneration)** and in-memory caching.
- **Scalability**: Optimized with the **Git Trees API** to handle hundreds of posts in a single request.

---

## 📂 File Structure

### 1. Types (`/types/blog.ts`)
Defines the structure for posts, metadata, pagination, and filters.

### 2. Core Logic (`/lib/githubBlog.ts`)
This is the "engine" that:
- Fetches all post slugs via the Trees API.
- Batches metadata fetching to avoid rate limits.
- Implements a 60-second in-memory cache.
- Handles server-side search, tag filtering, and pagination.

### 3. components
- `BlogCard.tsx`: The grid item for the listing page.
- `BlogLayout.tsx`: The wrapper for the single post page.
- `MarkdownRenderer.tsx`: Safely injects HTML with custom `prose-blog` styles.
- `BlogSearch.tsx`: Client-side search and tag filter bar.
- `BlogPagination.tsx`: Interactive pagination controls.

### 4. app Routes
- `app/blog/page.tsx`: The main listing page with search/pagination.
- `app/blog/[slug]/page.tsx`: The dynamic route for individual posts.
- `app/blog/[slug]/loading.tsx`: Skeleton loader for smooth transitions.
- `app/blog/[slug]/not-found.tsx`: Custom 404 for missing posts.
- `app/sitemap.ts`: Automatically adds all blog posts to your XML sitemap.

---

## 📝 How to Add a Blog Post

1.  **Open your GitHub Repo**: Go to `bhupi-010/bhupi-blogs`.
2.  **Add a File**: Create a new `.md` file in the `blogs/` folder.
3.  **Add Frontmatter**: Use the following format at the top:

```markdown
---
title: "Your Amazing Title"
description: "A short, catchy summary for SEO."
date: "2026-02-28"
tags: ["react", "frontend"]
cover: "https://images.unsplash.com/photo-xxx"
---

# Your Content Here
Support for **bold**, *italics*, and [links](https://google.com).

## Code Blocks
```javascript
console.log("Hello, World!");
\```
```

4.  **Save/Commit**: Once you push the change, your website will automatically update within **60 seconds** (due to ISR revalidation).

---

## 🔍 SEO Best Practices Integrated
- **JSON-LD**: Automatic `Article` and `Blog` structured data.
- **Dynamic Meta**: Titles and descriptions update based on search queries and tags.
- **Canonical URLs**: Prevents duplicate content issues.
- **Robots.txt**: Prevents crawlers from wasting crawl budget on search result pages.

---

## 🛠️ Configuration
If you ever change your GitHub username or repo name, update these constants in `lib/githubBlog.ts`:

```typescript
const GITHUB_USERNAME = 'bhupi-010';
const GITHUB_REPO = 'bhupi-blogs';
const POSTS_DIR = 'blogs';
```

---

