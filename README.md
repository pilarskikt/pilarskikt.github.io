# IT Journey — Jekyll blog on GitHub Pages

This site is built with **Jekyll** and deployed via **GitHub Actions** when you push to `main`.

## Adding a new post (auto-render)

1. Create a new file in `_posts/` with the naming pattern:
   ```text
   _posts/YYYY-MM-DD-your-post-title.md
   ```
   Example: `_posts/2025-03-18-my-new-post.md`

2. Add YAML front matter at the top (use suggested tags below for consistency):
   ```yaml
   ---
   title: Your post title
   date: 2025-03-18
   tags: [aws, terraform]
   ---
   ```

3. Write your content in Markdown below the front matter.

4. Commit and push to `main`. GitHub Actions will build the site and deploy it; your new post will appear automatically. No manual build step needed on your side.

## Local development (optional)

- **Ruby 3.0+** and Bundler are required.
- Run: `bundle install` then `bundle exec jekyll serve`
- Open http://localhost:4000

## GitHub Pages setup

In the repo **Settings → Pages**:

- **Source**: “GitHub Actions” (the workflow in `.github/workflows/pages.yml` builds and deploys the site).

If you previously used “Deploy from a branch”, switch to **GitHub Actions** so the Jekyll workflow runs on each push.

## Features (included)

- **Description & subtitle** – Set in `_config.yml`; used for SEO, feed, and tagline above the post list.
- **Reading time** – Shown in post meta (“X min read”), computed from word count.
- **Copy button** – Each code block has a “Copy” button (top-right); shows “Copied!” on success.
- **Dark code blocks** – Styled in `assets/css/code-blocks.css`; tune colors there if you like.
- **RSS** – Footer link to `/feed.xml` for subscribers.
- **Archives** – Nav link to `/archive/` (posts by year); active state in sidebar.

## UI enhancements

- **Skip to main content** – “Skip to main content” link (visible on keyboard focus) for accessibility.
- **Focus styles** – Visible outline on links/buttons when using keyboard (Tab).
- **Back to top** – Floating “↑” button at bottom-right on long pages; appears after you scroll down.
- **Smooth scroll** – In-page anchor links (e.g. tags) scroll smoothly.
- **Post list hover** – Post titles on the home page turn blue on hover.
- **Archive active state** – “Archives” in the nav is highlighted when you’re on the archive page.

## Suggested tags (blog focus: AWS, Terraform, OCI, OpenTofu)

Use these consistently so the Tags page and search stay useful:

| Focus | Tags to use |
|-------|-------------|
| **Cloud** | `aws`, `oci` |
| **IaC** | `terraform`, `opentofu` |
| **General** | `iac`, `automation`, `devops`, `howto`, `til` |

Examples: `[aws, terraform]`, `[oci, opentofu]`, `[aws, terraform, automation]`. Keep tags lowercase and short; add others (e.g. `python`, `vmware`) when the post is about them.

## Optional tweaks

- Edit `_config.yml` for `description`, `subtitle`, `author`.
- Add more nav links in `_includes/aircloud-nav.html`.
- Adjust code block colors in `assets/css/code-blocks.css` or copy-button style in `assets/css/enhancements.css`.
