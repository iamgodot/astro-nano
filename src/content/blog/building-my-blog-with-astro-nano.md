---
title: Building My Blog with Astro Nano
description: How I maintain blogs with Astro and its Nano theme
date: 2025-01-25 17:38
draft: false
---
Recently, I switched the SSG for my blog from Hugo to Astro. Hugo is pretty fast, but the documentation always seems a bit messy and outdated, on the other hand, Astro feels more modern with its well-organized tutorials.

## Astro Nano

For theme, I really like how Astro Nano being super lightweighted yet also with all the features I need. It retains Astro's core features while optimizing for minimal bundle size and maximum performance. For content-heavy sites like blogs, this provides several advantages:

- Faster build times
- Smaller output files
- Simplified configuration
- Excellent performance metrics

## Creating the Blog Structure

I structured my blog with the following directory layout:

```
my-nano-blog/
├── public/
│   ├── favicon.svg
│   └── assets/
├── src/
│   ├── components/
│   │   ├── Header.astro
│   │   ├── Footer.astro
│   │   └── PostCard.astro
│   ├── content/
│   │   └── blog/
│   │       ├── post-1.md
│   │       └── post-2.md
│   ├── layouts/
│   │   ├── BaseLayout.astro
│   │   └── PostLayout.astro
│   └── pages/
│       ├── index.astro
│       ├── blog/
│       │   └── [...slug].astro
│       └── about.astro
├── astro.config.mjs
├── package.json
└── tsconfig.json
```

### Setting Up Content Collections

Astro's content collections are perfect for managing blog posts. I configured them by creating a `src/content/config.ts` file:

```typescript
import { defineCollection, z } from 'astro:content';

const blogCollection = defineCollection({
  schema: z.object({
    title: z.string(),
    description: z.string(),
    publishDate: z.date(),
    tags: z.array(z.string()).optional(),
    image: z.string().optional(),
  }),
});

export const collections = {
  'blog': blogCollection,
};
```

### Creating Blog Posts

With collections configured, I create blog posts as Markdown files in the `src/content/blog/` directory:

```markdown
---
title: Getting Started with Astro Nano
description: Learn how to build lightning-fast websites with Astro Nano
publishDate: 2025-02-20
tags: ['astro', 'web development', 'performance']
image: '/assets/astro-banner.jpg'
---

# Getting Started with Astro Nano

Astro Nano provides an ultra-lightweight foundation for building static websites...

## Key Benefits

1. **Minimal JavaScript** - Only send JS when absolutely necessary
2. **Content-focused** - Perfect for blogs and documentation sites
3. **Blazing Fast** - Incredible load times and performance metrics

```

### Building the Blog Index Page

The homepage displays a list of blog posts:

```jsx
---
// src/pages/index.astro
import { getCollection } from 'astro:content';
import BaseLayout from '../layouts/BaseLayout.astro';
import PostCard from '../components/PostCard.astro';

const posts = await getCollection('blog');
const sortedPosts = posts.sort(
  (a, b) => b.data.publishDate.getTime() - a.data.publishDate.getTime()
);
---

<BaseLayout title="My Tech Blog">
  <h1>Welcome to My Blog</h1>
  
  <section class="posts-grid">
    {sortedPosts.map(post => (
      <PostCard post={post} />
    ))}
  </section>
</BaseLayout>
```

### Creating the Blog Post Template

Individual blog posts are rendered using a dynamic route:

```jsx
---
// src/pages/blog/[...slug].astro
import { getCollection } from 'astro:content';
import PostLayout from '../../layouts/PostLayout.astro';

export async function getStaticPaths() {
  const blogEntries = await getCollection('blog');
  return blogEntries.map(entry => ({
    params: { slug: entry.slug },
    props: { entry },
  }));
}

const { entry } = Astro.props;
const { Content } = await entry.render();
---

<PostLayout frontmatter={entry.data}>
  <Content />
</PostLayout>
```

## Deploying to Cloudflare Pages

Deploying the blog to Cloudflare Pages was straightforward:

1. Push the code to a GitHub repository
2. Log in to the Cloudflare dashboard
3. Navigate to Pages and click "Create a project"
4. Connect your GitHub repository
5. Configure the build settings:
   - Build command: `npm run build`
   - Build output directory: `dist`
   - Environment variables:
     - NODE_VERSION: 18

After the initial setup, Cloudflare automatically builds and deploys the site on each push to the main branch.

## Integrating with Editors

### For Writing Blogs

As I mentioned in another post, Obsidian has been my primary note-taking tool for years, thus it'd be amazing to use it for writing all my blogs. There's actually a very straightforward way to achieve this: symlinks. I just have to symlink the directories of those Markdown files to my Obsidian Vault, since it natively supports Markdown and file properties, I get to manage all the content in Obsidian. This way, not only can I use all of its handy features such as templating, but also it becomes much easier to reference my notes when writing blogs.

### For Coding

Since Astro provides code files as `*.astro`, a little setup is needed to get proper language support in Neovim.

For language parsing and code completion, we can simply add `astro` in the configuration of Treesitter and LSP. For formatting, a combination of `prettier` and a plugin called `prettier-plugin-astro` is pretty much all it takes to do the job, except that we also need to add a `.prettierrc` file and add:

```json
{
  "plugins": ["prettier-plugin-astro"]
}
```

---

### References

- [Astro Nano](https://github.com/markhorn-dev/astro-nano)
- [Prettier-plugin-astro](https://github.com/withastro/prettier-plugin-astro/issues/360)
- [Neovim Setup for AstroJs Projects](https://javascript.plainenglish.io/neovim-setup-for-astrojs-projects-ef8c405765ab)
