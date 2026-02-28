# Bhupi Blogs

Blog content for [bhupendranath.com.np](https://bhupendranath.com.np/blog).

Posts are stored as Markdown files in the `blogs/` directory and fetched dynamically by the portfolio's Next.js app — no redeployment needed.

## Adding a New Post

1. Create a new `.md` file in the `blogs/` folder
2. Add frontmatter at the top:

```markdown
---
title: "Your Post Title"
description: "A brief summary of the post"
date: "2026-01-15"
tags: ["tag1", "tag2"]
cover: "https://images.unsplash.com/photo-xxx?w=1200&h=630&fit=crop"
---

# Your content here...
```

3. Commit and push — the blog updates automatically within 60 seconds.

## Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `title` | ✅ | Post title |
| `description` | ✅ | Short summary for SEO |
| `date` | ✅ | Publication date (YYYY-MM-DD) |
| `tags` | ❌ | Array of tags for categorization |
| `cover` | ❌ | Cover image URL (recommended: 1200x630) |
