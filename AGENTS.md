# Personal Homepage Maintenance Notes

## Project Overview

This is a personal academic homepage customized from [PRISM](https://github.com/xyjoey/PRISM). It is built with Next.js and exported as a static site.

- Main content lives in `content/`.
- Chinese content lives in `content_zh/`.
- Static assets live in `public/`.
- Publication data is configured by `content/publications.toml`, which points to `content/publications.bib`.
- Journal covers and publication preview images live in `public/papers/`.
- Use `uv` for all Python-related tasks.

## Project Skills

Read the matching skill before running a long maintenance workflow. Keep this file as the routing layer and the skills as the procedural source of truth.

| Task | Skill |
| --- | --- |
| Add or update publications, Zotero metadata, journal covers, SCI quartiles, impact factors, or Google Scholar stats | `skills/homepage-publications/SKILL.md` |
| Build, verify, push, or debug GitHub Pages deployment | `skills/homepage-deployment/SKILL.md` |

## Content Source Of Truth

Use this table first when deciding which file to edit. Do not treat generated build output as the source of truth.

| Update target | Source file(s) | Asset folder | Verify page |
| --- | --- | --- | --- |
| Site metadata and footer date | `content/config.toml`, `content_zh/config.toml` | `public/` | Every page footer |
| About / bio | `content/about.toml`, `content_zh/about.toml`, `content/bio.md`, `content_zh/bio.md` | `public/` | `/`, `/about` |
| Publications | `content/publications.bib`, `content/publications.toml`, `content_zh/publications.toml` | `public/papers/` | `/publications` |
| Awards | `content/awards.toml`, `content_zh/awards.toml` | `public/awards/` | `/awards` |
| News | `content/news.toml`, `content_zh/news.toml` | `public/` | `/news` |
| Talks | `content/talks.toml`, `content_zh/talks.toml` | `public/` | `/talks` |
| Services | `content/services.toml`, `content_zh/services.toml` | `public/` | `/services` |

## Python And Scripts

Use `uv` for Python commands, including repo scripts and one-off Python checks.

Common script entrypoints:

```bash
npm run scholar:stats
npm run publication:ranks
npm run publication:accepted-dates
npm run content:check
```

Their Python commands are managed through `uv` in `package.json`.

## Bilingual Content Updates

Most visible content has both English and Chinese versions. When editing a file under `content/`, check the matching file under `content_zh/` in the same task, and vice versa.

- Keep structures aligned between English and Chinese TOML files whenever possible, including item order, section names, dates, links, image filenames, and visibility flags.
- Translate user-facing text only when the meaning is clear. If the wording is uncertain, keep the confirmed source-language update and mention the missing translation in the final response.
- Preserve shared identifiers and asset filenames exactly across languages.
- For publications, update the shared `content/publications.bib`; do not duplicate publication entries under `content_zh/`.


## Cross-Content Synchronization

Before editing, identify every public surface that shows the same real-world item. Do not leave conflicting versions unless the user explicitly requests a narrow update.

- Publications: `content/publications.bib` is the source of truth. For new publications or metadata changes, use `skills/homepage-publications/SKILL.md`.
- Awards: `content/awards.toml` and `content_zh/awards.toml` are the source of truth. When adding, deleting, renaming, redating, reordering, hiding, or unhiding an award, inspect `content/news.toml` and `content_zh/news.toml` in the same task.
## Award Dates And Ordering

Award source files use month-level dates even when the public display shows only the year.

- Store award dates in `content/awards.toml` and `content_zh/awards.toml` as `YYYY-MM`.
- Keep English and Chinese award item order aligned and sort awards by the stored `YYYY-MM` date from newest to oldest.
- Keep `content/news.toml` and `content_zh/news.toml` award news dates aligned with the matching award month when a corresponding news item exists or is added.
- The website awards page should display only the year for award dates. Do not change the TOML date back to `YYYY` just to alter display.

## Build And Verification

This project supports Node.js 22 through 25. The repository's `.node-version` and `.nvmrc` both specify `22`. For deployment builds, if the default `node -v` is `v26` or newer, `npm run build` will be rejected by `scripts/check-node-version.mjs`; use the Node 24 fallback documented in `skills/homepage-deployment/SKILL.md`.

Before finishing an ordinary content update:

- Run `git diff --stat` and review whether the changed files match the task scope.
- Check the relevant source files from the table above, including the matching `content/` or `content_zh/` file when the update is bilingual.
- Confirm referenced assets exist under `public/`, such as publication previews in `public/papers/` and award images in `public/awards/`.
- Run `npm run content:check`.
- If the task changes visual layout, navigation, or page placement, use `npm run dev` and check the affected page.

Do not run `npm run build` for ordinary content maintenance. `npm run build` performs the production static export and is reserved for deployment/publish/static-export verification, or when the user explicitly asks for a build/export.

If a dev server is running, do not run `npm run build` in parallel with it; both use `.next/`, and a build can break the dev server's temporary chunks. Stop dev before a deployment build, or after an accidental build cleanly restart dev by stopping it, removing generated `.next/`, and running `npm run dev` again.

Treat `content/config.toml` and `content_zh/config.toml` `last_updated` changes as normal `prebuild` side effects only when a deployment/static-export build is intentionally run.

## Git Hygiene

- Do not commit `.next/`, `out/`, `node_modules/`, `.venv/`, `.DS_Store`, or other generated local artifacts.
- When running `git add`, add only files changed in the current task that should enter version control.
- Do not manually commit `out/`; GitHub Pages deploys from the static export generated in CI.
