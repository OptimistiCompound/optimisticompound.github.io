# Yong Zhong's personal site

Source for [optimisticompound.github.io](https://optimisticompound.github.io), built with the pinned [`al-folio v1.1`](https://github.com/alshedivat/al-folio/releases/tag/v1.1) starter and deployed by GitHub Actions.

## Content map

| Content                    | Location                     |
| -------------------------- | ---------------------------- |
| Homepage biography         | `_pages/about.md`            |
| Blog posts                 | `_posts/YYYY-MM-DD-title.md` |
| Project cards              | `_projects/*.md`             |
| Social links               | `_data/socials.yml`          |
| GitHub repository cards    | `_data/repositories.yml`     |
| Site metadata and features | `_config.yml`                |
| Site-specific styling      | `_sass/_custom.scss`         |

The theme runtime is supplied by version-pinned Ruby gems. Local layout/style overrides are intentionally small and tracked with the al-folio upgrade audit so future upgrades remain reviewable.

## Local preview

Docker is the recommended development environment:

```bash
docker compose pull
docker compose up
```

Open <http://localhost:8080>. Changes are rebuilt automatically.

For a native setup with Ruby 3.3.5:

```bash
bundle install
npm ci
bundle exec jekyll serve
```

## Add content

Create a post in `_posts/` with front matter like:

```yaml
---
layout: post
title: My post
date: 2026-08-05 12:00:00 +0800
description: One concise sentence used in post cards and search.
tags: systems notes
categories: notes
---
```

Create a project card in `_projects/` using `title`, `description`, `importance`, `github`, and an optional `redirect` URL. No theme code needs to change for routine content updates.

## Validate and deploy

```bash
npm ci
npm run lint:prettier
bundle exec al-folio upgrade audit --no-fail
bundle exec al-folio upgrade overrides audit
bundle exec jekyll build
```

Pushes to `main` run `.github/workflows/deploy.yml`, build `_site`, and publish the generated files to the `gh-pages` branch. Do not edit `gh-pages` manually.

## Upgrade

Update the exact `al_*` gem pins in `Gemfile`, then run:

```bash
bundle update
bundle exec al-folio upgrade audit
bundle exec al-folio upgrade overrides audit
bundle exec al-folio upgrade report
```

Review upstream changes before accepting drift in local overrides.
