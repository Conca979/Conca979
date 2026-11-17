# Conca979 GitHub Profile

Minimal terminal-inspired GitHub profile repository (`Conca979/Conca979`).

## Repository Layout

- `README.md`: Profile entry point embedding the terminal header, generated stats card, and contact badges.
- `assets/terminal-header.svg`: Locally committed animated SVG terminal card (pure SVG + CSS keyframes, no scripts or external fonts).
- `.github/workflows/readme-stats.yml`: Daily scheduled and manually dispatchable workflow using `stats-organization/github-readme-stats-action@v2` to generate `profile/stats.svg`.
- `references.md`: Upstream reference to `stats-organization/github-stats-extended`.

## Design Tokens (Cosmic Terminal)

- Background: `#090a0f`
- Title bar / badge label: `#111420`
- Border: `#1f2638`
- Primary accent: `#f59e0b`
- Secondary accent: `#38bdf8`
- Primary text: `#e6edf3`
- Muted text: `#8b949e`

## Conventions

- Keep `assets/terminal-header.svg` compatible with GitHub's `<img>` CSP sandbox (system monospace fonts only, no `<script>`, no `<foreignObject>`, include `prefers-reduced-motion` fallback).
- Keep all path references relative to the repository root.
- Stats card generation uses `${{ secrets.GITHUB_TOKEN }}` with public repository scope and `include_all_commits=true`.
