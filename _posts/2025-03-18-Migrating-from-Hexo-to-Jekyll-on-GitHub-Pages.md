---
title: Migrating my blog from Hexo to Jekyll on GitHub Pages
description: Real-life migration from Hexo to Jekyll—why I did it, what changed, and how publishing works now.
date: 2025-03-18
tags: [jekyll, github-pages, hexo, migration, blog]
---

I had been running this blog on **Hexo** for years. It worked, but I kept hearing that **Jekyll** is the natural fit when you host on **GitHub Pages**—and that publishing could be as simple as pushing a Markdown file. I decided to migrate and document what the move actually looked like in practice.

## Why Jekyll on GitHub Pages?

- **Native support** – GitHub builds Jekyll when you push; no separate “deploy” step or Node/npm in CI if you don’t want it.
- **Publish by pushing** – Add a file under `_posts/` with the right name and front matter, push to `main`, and the post goes live after the build. No `hexo generate` or uploading a `public/` folder.
- **One stack** – Content, config, and theme live in the same repo; no split between “source” and “built output” in my head.

So the goal was: same blog, same look (I kept my previous theme’s style), but on Jekyll and with a simpler workflow.

## What we actually did

### 1. New Jekyll layout

- Added **`_config.yml`** (site title, URL, permalinks, theme, Giscus).
- Created **`_posts/`** and moved the existing posts from Hexo’s `source/_posts/` into it. Post filenames: `YYYY-MM-DD-slug.md`. Front matter stayed similar: `title`, `date`, `tags`; Hexo-only things like {% raw %}`{% codeblock %}`{% endraw %} were turned into normal fenced code blocks.

### 2. Theme and look

- I didn’t want to lose the look I had. So we kept a **dark, sidebar layout** and recreated it in Jekyll with custom layouts (`_layouts/base.html`, `home`, `post`, `page`) and the existing CSS (copied and wired up as the main stylesheet). Sidebar nav, avatar, post list, and single-post layout all match the old Hexo theme.

### 3. Comments and extras

- **Giscus** was already in use. We dropped the script into an include and kept the same repo/category IDs, so comments stayed intact.
- **RSS** – Jekyll’s feed is at `/feed.xml`; we linked it in the footer.
- **Tags and archive** – A tags page and an archive page (posts by year) with the same URLs as before where it made sense.

### 4. Build and deploy

- **GitHub Actions** runs `bundle exec jekyll build` and deploys the `_site` output to GitHub Pages. So the repo is “source only”; the build happens on push. No Hexo, no Node, no `package.json` in this repo anymore.

### 5. Cleanup

- Removed the old Hexo bits: `source/`, `themes/`, `scaffolds/`, `package.json`, and related config. **Dependabot** was still opening npm/Hexo PRs, so we switched `.github/dependabot.yml` to use only the **bundler** (Ruby) ecosystem for the Gemfile. That stopped the noise.

## How I publish a post now

1. Create **`_posts/YYYY-MM-DD-my-post-title.md`**.
2. Add YAML front matter at the top:

   ```yaml
   ---
   title: My post title
   date: 2025-03-18
   tags: [tag1, tag2]
   ---
   ```

3. Write the body in Markdown.
4. Commit and push to `main`.

The Actions workflow builds the site and deploys it; the new post appears without running any local build. That “just add a file and push” flow was the main win.

## Takeaways

- **Jekyll + GitHub Pages** really does simplify things if you’re okay with Ruby in CI and Markdown as the source of truth.
- Migrating from Hexo is mostly: move posts into `_posts/` with Jekyll-style front matter, fix any theme-specific syntax (e.g. code blocks), then replicate the layout and CSS in Jekyll. Giscus (or similar) can stay as-is.
- Turning off Dependabot for npm in a repo that no longer uses it saves a lot of irrelevant PRs.

If you’re on Hexo and considering the same move, I hope this gives you a concrete picture of what the migration looks like in a real setup.
