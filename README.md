# macagr.github.io

Source for [randombyte's blog](https://macagr.github.io) — a Jekyll site built
on the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) theme and
deployed to GitHub Pages via GitHub Actions.

## Structure

```
.
├── _posts/                  Published posts (YYYY-MM-DD-title.md)
├── _drafts/                 Work-in-progress posts (not published)
├── _tabs/                   Top-nav pages (About, Tags, Categories, Archives)
├── assets/images/           Post images
├── _config.yml              Site configuration
├── Gemfile                  Ruby dependencies
├── index.html               Home (auto-lists posts via Chirpy's home layout)
└── .github/workflows/       CI: build, link-check, deploy
```

## Local development

Requirements: Ruby 3.x and Bundler.

```bash
bundle install
bundle exec jekyll serve --livereload
```

Preview drafts with:

```bash
bundle exec jekyll serve --drafts
```

## Writing a new post

Create a file in `_posts/` named `YYYY-MM-DD-slug.md` with front matter:

```yaml
---
title: "Your title"
date: 2026-01-15 12:00:00 +0000
categories: [Primary, Secondary]
tags: [tag1, tag2]
---
```

Push to `master` — GitHub Actions builds and deploys automatically. The post
will appear on the home page, tags, categories, and archives with no manual
index editing required.

## Deployment

`.github/workflows/pages-deploy.yml` handles everything:

1. Builds the site with Jekyll in production mode
2. Runs HTMLProofer to catch broken internal links and missing images
3. Deploys to GitHub Pages via the official actions

Set the Pages source to **GitHub Actions** in repository settings.

## License

Content: © RandomByte. Theme: MIT (Chirpy).
