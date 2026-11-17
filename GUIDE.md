# Custom GitHub Profile Guide

This guide explains how custom GitHub profile pages work, the platform constraints they operate under, and how this repository (`Conca979/Conca979`) is structured and maintained.

---

## 1. How a GitHub Profile Repository Works

GitHub treats a repository as your profile README when all four conditions are met:

1. The repository name matches your GitHub username (`Conca979/Conca979`).
2. The repository is **public**.
3. A `README.md` file exists at the root of the default branch (`main`).
4. The `README.md` file is non-empty.

When active, GitHub renders `README.md` at the top of `https://github.com/Conca979`, above your Pinned repositories and Contribution graph. If you ever want to hide it temporarily, rename `README.md` or make the repository private.

---

## 2. Repository Layout

- `README.md`: Profile entry point rendered on GitHub using `<picture>` blocks to switch between dark and light assets (`assets/terminal-header*.svg`, `profile/stats*.svg`, and contact badges).
- `assets/terminal-header.svg` and `assets/terminal-header-light.svg`: Custom `800x210` animated SVG terminal cards committed directly to the repo for dark and light GitHub themes.
- `profile/stats.svg` and `profile/stats-light.svg`: Static GitHub stats SVGs generated and committed by GitHub Actions.
- `.github/workflows/readme-stats.yml`: Automated workflow that regenerates both `profile/stats.svg` and `profile/stats-light.svg` daily and on manual dispatch.
- `references.md`: Link to the upstream `stats-organization/github-stats-extended` project.
- `AGENTS.md`: Project conventions and Cosmic Terminal design tokens.
- `GUIDE.md`: This reference document.

---

## 3. GitHub Markdown and SVG Rules

GitHub renders `README.md` inside a strict security sandbox. Knowing what is allowed prevents broken layouts or stripped elements.

### Allowed HTML in `README.md`

- Basic layout tags and alignment attributes such as `<p align="center">`, `<a href="...">`, and `<img src="..." width="800" />` are supported.
- Theme-aware images can use `<picture>` with `<source media="(prefers-color-scheme: dark)" srcset="...">` and `<source media="(prefers-color-scheme: light)" srcset="...">` if you ever want separate dark and light variants.
- `<script>`, `<iframe>`, `<style>` blocks inside Markdown, and inline `style="..."` attributes are stripped by GitHub.

### SVG Constraints (`<img>` CSP Sandbox)

When an SVG is embedded in `README.md`, GitHub serves it inside an `<img>` element (and proxies external URLs through GitHub Camo):

- **No scripts or interactive JS**: `<script>` tags and event handlers (`onload`, `onclick`) are blocked.
- **No external fonts or network requests**: `@import url(...)` and external stylesheets inside the SVG are blocked. Always use local system font stacks (this repo uses `ui-monospace, 'SFMono-Regular', Menlo, Monaco, Consolas, 'Liberation Mono', monospace`).
- **Avoid `<foreignObject>`**: Pure SVG elements (`<rect>`, `<text>`, `<tspan>`, `<path>`, `<circle>`, `<ellipse>`, `<clipPath>`) render reliably across browsers and proxies.
- **CSS `@keyframes` work**: Pure CSS animations (like the line reveal and blinking cursor in `assets/terminal-header.svg`) run inside `<img>`. Include `@media (prefers-reduced-motion: reduce)` so users who disable motion see static text immediately.

### Image Caching

- **Local repository SVGs** (`./assets/terminal-header.svg`, `./profile/stats.svg`) update as soon as a new commit lands on `main`.
- **External badge services** (such as `img.shields.io`) are cached by GitHub's Camo proxy, so changes to badge query strings may take a short time to refresh on the profile page (though changing the URL itself busts the cache).

---

## 4. Automated Stats Card (`github-stats-extended`)

Instead of calling a live hosted URL on every page view (which can hit shared rate limits or fail to load), this repo uses `stats-organization/github-readme-stats-action@v2` in `.github/workflows/readme-stats.yml` to build a static `profile/stats.svg`.

### Workflow Triggers

- `schedule: - cron: "0 0 * * *"`: Runs automatically once per day at `00:00 UTC`.
- `workflow_dispatch`: Lets you trigger an immediate refresh anytime from the **Actions** tab or with `gh workflow run readme-stats.yml`.

### Current Card Options

In `.github/workflows/readme-stats.yml`, `options` is passed as a query string:

```text
username=${{ github.repository_owner }}&show_icons=true&include_all_commits=true&rank_icon=github&bg_color=090a0f&border_color=1f2638&title_color=f59e0b&icon_color=38bdf8&text_color=e6edf3&ring_color=f59e0b
```

Key parameters:

- `show_icons=true`: Displays metric icons next to stars, commits, PRs, issues, and repos.
- `include_all_commits=true`: Counts all-time commits instead of only the current calendar year.
- `rank_icon=github`: Uses the GitHub Octocat icon inside the rank ring.
- `bg_color`, `border_color`, `title_color`, `icon_color`, `text_color`, `ring_color`: Hex colors (without `#`) matching the Cosmic Terminal palette.

### Public vs. Private Contributions

By default, the workflow passes `token: ${{ secrets.GITHUB_TOKEN }}`. This works with zero configuration, requires no manual secrets, and counts **public** repository activity only.

If you later want the stats card to include **private** repository contributions:

1. Create a GitHub Personal Access Token (PAT) with `repo` and `read:user` scope (or a fine-grained token with read access to your repositories).
2. Add it in your repository under **Settings -> Secrets and variables -> Actions -> New repository secret** as `STATS_TOKEN`.
3. In `.github/workflows/readme-stats.yml`, update `token:` to `${{ secrets.STATS_TOKEN }}` and append `&count_private=true` to `options`.

### Adding More Stat Cards Later

`github-readme-stats-action@v2` also supports `card: top-langs` (top languages) and `card: pin` (repository cards). To add one:

1. Add another step in `.github/workflows/readme-stats.yml` with a separate `card` and `path` (for example `profile/top-langs.svg`).
2. Update the commit step to `git add profile/*.svg` (or add the new file path explicitly).
3. Embed `./profile/top-langs.svg` in `README.md`.

---

## 5. Customizing the Design

### Cosmic Terminal Palette

All visual components share the same hex tokens so the header, stats card, and badges look like one unit on both Light and Dark GitHub themes:

| Role | Hex | Usage |
| :--- | :--- | :--- |
| Background | `#090a0f` | Terminal window, stats card background, badge value fill |
| Surface | `#111420` | Terminal title bar, badge label fill |
| Border | `#1f2638` | Window border, title divider, stats card border |
| Primary accent | `#f59e0b` | Prompt (`$`), cursor, accretion ring, stats title and rank ring |
| Secondary accent | `#38bdf8` | Command names, orbital accent, stats icons, Facebook badge |
| Primary text | `#e6edf3` | Output text and stats values |
| Muted text | `#8b949e` | Window title, subtitles, file paths |

### Editing `assets/terminal-header.svg`

- **Text lines**: Edit the `<text>` / `<tspan>` elements at the bottom of `assets/terminal-header.svg`. Keep lines concise so they stay left of the right-side black-hole motif (which sits around `x=600..770`).
- **Animation timing**: Adjust the `.reveal-1` through `.reveal-4` `animation` delays/durations in `<style>`, or remove the `<clipPath>` attributes if you prefer instant static text.
- **Dimensions**: Keep `viewBox="0 0 800 210"` in sync with `width` and `height` if you resize the card.

### Editing Contact Badges in `README.md`

Badges use `img.shields.io` with `style=flat-square`:

```text
https://img.shields.io/badge/<LABEL>-<MESSAGE>-<MESSAGE_BG>?style=flat-square&labelColor=<LABEL_BG>&color=<ACCENT_OR_RIGHT_COLOR>
```

Note on Shields.io syntax:

- `-` separates label, message, and right-side background color; use `--` for a literal hyphen, `_` for a space, and URL-encode special characters like `@` (`%40`).
- `labelColor=111420` sets the left half background; `color=f59e0b` / `color=38bdf8` sets the right half background; adding `090a0f` as the third path segment is overridden when `color=` is also supplied, or you can omit `color=` if you want a `#090a0f` right-side background with a colored accent.

---

## 6. Complementary GitHub Profile Settings (Outside `README.md`)

A great GitHub profile pairs a clean `README.md` with GitHub's native sidebar and overview controls:

- **Pinned repositories**: Use **Customize your pins** on your GitHub profile to showcase up to 6 flagship repositories right below `README.md` (no need to duplicate them inside a minimal README).
- **Profile sidebar**: Edit your display name, bio, location, company/organization, email, and social links in **GitHub Settings -> Profile**.
- **Contribution settings**: Toggle whether private contributions appear in your activity graph via **Contribution settings** above the calendar grid.
- **Workflow permissions**: In repository **Settings -> Actions -> General -> Workflow permissions**, ensure `Read and write permissions` is enabled (or declared via `permissions: contents: write` in the workflow, as `.github/workflows/readme-stats.yml` already does).

---

## 7. Day-to-Day Git Workflow

Because the daily GitHub Action commits `profile/stats.svg` directly to `main`, always pull before making local edits so your local branch stays in sync:

```bash
git pull --ff-only origin main
```

Useful commands with GitHub CLI (`gh`):

```bash
# Manually regenerate profile/stats.svg right now
gh workflow run readme-stats.yml

# Check recent workflow runs
gh run list --limit 5

# Inspect the failed/latest run log if an update fails
gh run view --log
```
