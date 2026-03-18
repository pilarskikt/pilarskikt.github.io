# IT Journey — Jekyll blog on GitHub Pages

This site is built with **Jekyll** and deployed via **GitHub Actions** when you push to `main`.

## Adding a new post (auto-render)

1. Create a new file in `_posts/` with the naming pattern:
   ```text
   _posts/YYYY-MM-DD-your-post-title.md
   ```
   Example: `_posts/2025-03-18-my-new-post.md`

2. Add YAML front matter at the top:
   ```yaml
   ---
   title: Your post title
   date: 2025-03-18
   tags: [tag1, tag2]
   ---
   ```

3. Write your content in Markdown below the front matter.

4. Commit and push to `main`. GitHub Actions will build the site and deploy it; your new post will appear automatically. No manual build step needed on your side.

## Local development (optional)

- **Ruby 3.0+** and Bundler are required.
- Run: `bundle install` then `bundle exec jekyll serve`
- Open http://localhost:4000

## Migration from Hexo

This repo was migrated from Hexo to Jekyll. Old Hexo files (`source/`, `themes/`, `scaffolds/`, `package.json`) are still present but are excluded from Jekyll. You can remove them later if you no longer need them.

## GitHub Pages setup

In the repo **Settings → Pages**:

- **Source**: “GitHub Actions” (the workflow in `.github/workflows/pages.yml` builds and deploys the site).

If you previously used “Deploy from a branch”, switch to **GitHub Actions** so the Jekyll workflow runs on each push.
