# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal academic website for Junhao Liu (PKU), built with Jekyll and the al-folio theme. Hosted on GitHub Pages at https://outerform.site.

## Build & Development Commands

```bash
# Local dev server (with Docker)
docker-compose up                    # serves on port 8080 with live reload

# Local dev server (without Docker)
bundle exec jekyll serve --watch --port=8080 --host=0.0.0.0 --livereload

# Production build
JEKYLL_ENV=production bundle exec jekyll build

# CSS purging (post-build optimization)
npm install -g purgecss && purgecss -c purgecss.config.js

# Full deploy (interactive script)
bin/deploy

# Code formatting
npx prettier --write .
```

## CI/CD

GitHub Actions (`.github/workflows/deploy.yml`) triggers on push to main/master. It builds with Ruby 3.3, runs purgecss, and deploys via GitHub Pages.

## Architecture

### Publications (`_bibliography/papers.bib` + `_layouts/bib.liquid`)

The publication system is the most complex part of the site. Papers are defined as BibTeX entries with custom fields:

- **`abbr`**: Venue badge label (e.g., `AAAI`, `TOPLAS`, `Preprint`). Colors/URLs configured in `_data/venues.yml`.
- **`selected={true}`**: Shows on homepage under "selected publications".
- **`preview`**: Filename in `assets/img/publication_preview/` for thumbnail.
- **`award` / `award_name`**: Award badge (e.g., `Oral (4.68%)`).
- **`annotation`**: Tooltip text shown via info icon next to authors (e.g., `* Equal contribution`).
- **Links**: `pdf`, `code`, `website`, `blog`, `video`, `poster`, `slides`, `supp`, `arxiv`.
- **Badges**: `google_scholar_id`, `altmetric`, `dimensions`, `inspirehep_id`.

**Author superscripts**: Characters `*∗†‡§¶‖&^` in author last names are rendered as `<sup>` tags by `bib.liquid` (line 62-63). Use `Yan*, Zhenyu` to show "Zhenyu Yan*" with superscript. The `_plugins/hide-custom-bibtex.rb` strips these from the raw BibTeX display.

**Coauthor linking**: Author last names are matched against `_data/coauthors.yml` (lowercased, accents removed) to auto-link to coauthor websites. Self-author (configured in `_config.yml` under `scholar.last_name/first_name`) is rendered in `<em>`.

### News (`_news/announcement_N.md`)

Sequential markdown files with YAML front matter:
```yaml
---
layout: post
date: YYYY-MM-DD
inline: true
related_posts: false
---
```
- `inline: true`: Content rendered directly in the news table on homepage.
- `inline: false`: Title becomes a clickable link to a full post page.
- Displayed in reverse chronological order, limited by `announcements.limit` in `_config.yml` (currently 5).

### Key Directories

- **`_layouts/`**: 14 Liquid templates. `bib.liquid` (370 lines) handles all publication rendering.
- **`_includes/`**: 26 components. Key: `news.liquid`, `selected_papers.liquid`, `header.liquid`, `footer.liquid`.
- **`_plugins/`**: 9 custom Ruby plugins. Notable: `hide-custom-bibtex.rb` (strips custom fields), `google-scholar-citations.rb` (fetches citation counts).
- **`_data/`**: `coauthors.yml` (author links), `cv.yml` (CV content), `venues.yml` (venue badge config).
- **`_pages/`**: `about.md` (homepage), `publications.md`, `cv.md`.

### Image Handling

- Publication previews go in `assets/img/publication_preview/`.
- ImageMagick auto-generates responsive WebP variants (480, 800, 1400px).
- Use `{% include figure.liquid %}` for responsive images in templates.

## Conventions

- BibTeX keys: `authorYYYYkeyword` (e.g., `liu2025rex`, `li2026loris`)
- News files: `announcement_N.md` with sequential numbering
- Preview images: named to match the paper/tool (e.g., `loris.jpg`, `conlux.jpg`)

## Important Rules

- **CV sync**: The web CV (`assets/json/resume.json`) MUST stay in sync with the PDF CVs (`assets/tex/CV_en.tex` → `assets/pdf/CV.pdf`, `assets/tex/CV_zh.tex` → `assets/pdf/CV_zh.pdf`). When updating CV content, always update all three: tex sources, compiled PDFs, and resume.json.
- **Project Up = 青云计划**: Tencent internship program "Project Up" is called "青云计划" in Chinese.
- **News only for accepted papers**: News announcements are only created for papers accepted at conferences/journals, not for preprints.
- **Publications ordering**: First-author papers are listed before non-first-author papers within each section/year group.
- **Publications page split**: The publications page separates formally published papers from preprints using `{% bibliography --query %}` filters.
