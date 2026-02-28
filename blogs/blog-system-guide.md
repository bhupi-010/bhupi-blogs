---
title: "Architecture: My Next.js + GitHub Dynamic Blog System"
description: "A deep dive into how this blog functions as a headless CMS using the GitHub Trees API, ISR, and Tailwind Typography."
date: "2026-02-28"
tags: ["nextjs", "github", "architecture", "cms"]
cover: "https://images.unsplash.com/photo-1618477388954-7852f32655ec?w=1200&h=630&fit=crop"
---

# Dynamic Blog System Guide (Next.js + GitHub)

This blog doesn't use a traditional database. Instead, it leverages **GitHub as a Headless CMS**. This approach allows for a Git-based workflow where writing a post is as simple as pushing a Markdown file to a repository.

## 🚀 Architecture Overview

The system is built on the **Next.js App Router** and high-performance data fetching patterns:

* **Content Store**: A dedicated repository (`bhupi-blogs`) acting as the source of truth.
* **Data Fetching**: Uses the **GitHub Trees API** to recursively fetch file structures in a single request, minimizing API overhead.
* **Parsing**: Markdown is processed via `gray-matter` for metadata and `remark/rehype` for HTML conversion.
* **Caching Strategy**: Implements **Incremental Static Regeneration (ISR)** with a 60-second revalidation window to ensure high speed without stale content.



---

## 📂 System Structure

### 1. Core Logic (`/lib/githubBlog.ts`)
The "Engine" of the blog. It handles:
* Fetching slugs and batching metadata to stay within GitHub's rate limits.
* Server-side logic for **Search**, **Tag Filtering**, and **Pagination**.
* In-memory caching to prevent redundant network requests.

### 2. UI Components
* `MarkdownRenderer.tsx`: A secure wrapper that injects parsed HTML with custom `prose-blog` styles for beautiful typography.
* `BlogSearch.tsx`: A hybrid component that manages search queries and category filters via URL state.
* `BlogPagination.tsx`: Handles dynamic page routing for large content sets.

### 3. Smart Routing
* `app/blog/[slug]/page.tsx`: The dynamic segment that fetches and renders individual posts.
* `app/sitemap.ts`: A dynamic script that automatically injects new blog posts into your XML sitemap for instant Google indexing.

---

## 📝 How to Publish a New Post

Publishing is fully decoupled from the main application code. To add content:

1.  **Navigate** to your content repo: `bhupi-010/bhupi-blogs`.
2.  **Create** a new `.md` file inside the `blogs/` folder.
3.  **Define Frontmatter**: Ensure the top of your file looks like this:

```markdown
---
title: "Your Post Title"
description: "A concise summary for SEO results."
date: "2026-02-28"
tags: ["nextjs", "webdev"]
cover: "[https://images.unsplash.com/photo-xxx](https://images.unsplash.com/photo-xxx)"
---

# Start Writing...
Your Markdown content goes here.
