# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Nexus Systems Blog - A Jekyll-based static site using the Chirpy theme (v7.4.1), customized with Nexus Systems branding. Deployed to GitHub Pages at blog.nexus-systems.io.

## Common Commands

```bash
# Install dependencies
bundle install

# Local development with live reload
./tools/run.sh

# Production build with HTML validation
./tools/test.sh

# Manual commands
bundle exec jekyll serve -l    # Dev server with live reload
bundle exec jekyll build       # Build static site
bundle exec htmlproofer _site  # Validate HTML output
```

VSCode tasks are available: "Run Jekyll Server" and "Build Jekyll Site".

## Architecture

### Content Structure
- `_posts/` - Blog posts in Markdown with YAML front matter (format: `YYYY-MM-DD-slug-title.md`)
- `_drafts/` - Unpublished draft posts
- `_tabs/` - Navigation pages (About, Archives, Categories, Tags)
- `_data/` - Configuration data (authors.yml, contact.yml, share.yml)

### Styling
- `assets/css/jekyll-theme-chirpy.scss` - Complete custom Nexus branding overrides
- Nexus color palette defined in _config.yml under `nexus:` section
- Primary colors: Navy (#002060), Secondary Blue (#4472C4), Bright Blue (#3B82F6)

### Customizations
- `_plugins/posts-lastmod-hook.rb` - Adds `last_modified_at` metadata from git history
- `_config.yml` - Contains custom `nexus:` settings for brand colors and company info

## Blog Post Front Matter

```yaml
---
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS -0800
categories: [Category1, Category2]
tags: [tag1, tag2]
author: david              # References _data/authors.yml
pin: false                 # Pin to top of homepage
toc: true                  # Table of contents
image:
  path: /assets/img/posts/image.png
  alt: "Description"
---
```

## Authors Configuration

Authors are defined in `_data/authors.yml`. When using `author: david` in post front matter, it looks up the author details from this file:

```yaml
david:
  name: David Radtke
  url: https://github.com/dradtke08
```

## CI/CD

GitHub Actions workflow (`.github/workflows/pages-deploy.yml`) automatically builds and deploys on push to main. Build process:
1. Jekyll build in production mode
2. HTML validation (internal links only)
3. Deploy to GitHub Pages

## Logo Assets

- `assets/img/nexus-logo.png` - Cropped logo with "Nexus Systems" text (used for avatar, about page, post featured images)
- `assets/img/nexus-logo-full.png` - Full logo with extra margins (legacy, not currently used)

## Completed Customizations (December 2025)

### Dark Mode Support
The Chirpy theme uses two methods for dark mode:
1. `html[data-mode="dark"]` - Manual toggle via the theme switcher button
2. `@media (prefers-color-scheme: dark)` with `html:not([data-mode])` - System-level preference

**Important**: When adding dark mode CSS, you MUST include BOTH selectors to cover all cases.

Example pattern:
```scss
/* Manual dark mode toggle */
html[data-mode="dark"] .your-selector {
  /* dark styles */
}

/* System-level dark mode preference */
@media (prefers-color-scheme: dark) {
  html:not([data-mode]) .your-selector {
    /* same dark styles */
  }
}
```

### CSS Changes Made

1. **Sidebar Avatar** - Changed from circular (`border-radius: 50%`) to rounded square (`border-radius: 8px`) to show full logo including "Nexus Systems" text

2. **Sidebar Alignment** - Fixed mobile/responsive view left alignment with `align-items: flex-start`

3. **Post Card Featured Image**:
   - Removed forced `aspect-ratio: 40/21` that was clipping images
   - Set `aspect-ratio: auto` and proper `max-height: 220px`
   - Added `border-radius: 8px` to match avatar styling
   - Made container background transparent, dark mode card background is `#2d2d2d`

4. **Dark Mode Text Colors**:
   - h1/.card-title: `var(--nexus-cyan)`
   - h2: `var(--nexus-bright-blue)` with blue border
   - h3: `var(--nexus-cyan)`
   - Body text/paragraphs: `#d0d0d0`

5. **GLightbox Popup Styling** - Added dark mode styling for images opened in the lightbox popup (rounded corners, shadow, border)

6. **Base Theme Import** - The SCSS file MUST include `@use 'main';` at the top to import the base Chirpy theme, otherwise responsive behaviors break and duplicate icons appear

### Files Modified

- `assets/css/jekyll-theme-chirpy.scss` - Main customization file (all CSS overrides)
- `_tabs/about.md` - Changed to use cropped logo (`nexus-logo.png`) at width 200
- `_posts/2025-12-03-why-nexus-systems-is-ai-native.md` - Changed featured image to `nexus-logo.png`
- `_data/authors.yml` - Created to define author "david" for post bylines

## Completed Enhancements (December 2025 - Session 2)

- [x] **Sidebar avatar styling** - Added bright blue border and glow effect (`border: 2px solid var(--nexus-bright-blue)`, `box-shadow: 0 4px 16px rgba(59, 130, 246, 0.4)`)
- [x] **Post card image alignment** - Centered logo in column with `padding: 0`, `margin: 0 auto`
- [x] **Search box styling** - Softened border to `rgba(68, 114, 196, 0.3)`, added focus effects with transition
- [x] **Sidebar tagline** - Increased font size from `0.875rem` to `1rem` with `line-height: 1.4`
- [x] **Hover effects** - Added subtle lift and shadow on post cards (`transform: translateY(-2px)`, increased `box-shadow`)

## Future Enhancements (TODO)

### Content/UX
- [ ] **Feature images** - Create proper banner-style hero images for posts (the logo creates large empty space on post pages)
- [ ] **Light mode testing** - Verify light mode still looks good after dark mode changes

### Known Issues
- **Post page featured image** - When using the company logo as featured image, it displays small with large empty off-white space above the content. Consider using banner-style images or removing the featured image for posts that don't have a custom image.

## Troubleshooting

### CSS Changes Not Taking Effect
1. Multiple Jekyll servers may be running - kill all Ruby processes: `Stop-Process -Name ruby -Force` (PowerShell)
2. Browser caching - use Ctrl+Shift+R or open in incognito
3. Check generated CSS in `_site/assets/css/jekyll-theme-chirpy.css` to verify changes compiled

### Windows Reserved Filename Issue
If a `nul` file gets created (Windows reserved device name), delete with:
```powershell
Remove-Item -LiteralPath '\\?\C:\Repos\nexus-systems-blog\_site\nul' -Force
```

### Duplicate Icons in Topbar
This happens when `@use 'main';` is missing from the top of `jekyll-theme-chirpy.scss`. The base Chirpy theme must be imported for responsive behaviors to work correctly.