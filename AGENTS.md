# Conca979 GitHub Profile

Minimal terminal-inspired GitHub profile repository (`Conca979/Conca979`).

## Repository Layout

- `README.md`: Profile entry point using `<picture>` (`prefers-color-scheme: dark` / `light`) to swap dark and light variants of the terminal header and stats card.
- `assets/terminal-header.svg`: Dark-mode animated SVG terminal card (pure SVG + CSS keyframes, no scripts or external fonts).
- `assets/terminal-header-light.svg`: Light-mode animated SVG terminal card matching GitHub Light surfaces.
- `.github/workflows/readme-stats.yml`: Daily scheduled and manually dispatchable workflow using `stats-organization/github-readme-stats-action@v2` to generate `profile/stats.svg` (dark) and `profile/stats-light.svg` (light).
- `GUIDE.md`: Reference documentation covering how GitHub profile repositories work, SVG/Markdown constraints, and customization steps.
- `references.md`: Upstream reference to `stats-organization/github-stats-extended`.

## Design Tokens (Cosmic Terminal)

### Dark Variant

- Background: `#090a0f`
- Title bar / badge label: `#111420`
- Border: `#1f2638`
- Primary accent: `#f59e0b`
- Secondary accent: `#38bdf8`
- Primary text: `#e6edf3`
- Muted text: `#8b949e`

### Light Variant

- Background: `#ffffff`
- Title bar / badge label: `#f1f5f9`
- Border: `#d0d7de`
- Primary accent: `#d97706`
- Secondary accent: `#0284c7`
- Primary text: `#1f2328`
- Muted text: `#656d76`

## Conventions

- Keep `assets/terminal-header.svg` and `assets/terminal-header-light.svg` compatible with GitHub's `<img>` CSP sandbox (system monospace fonts only, no `<script>`, no `<foreignObject>`, include `prefers-reduced-motion` fallback).
- Keep all path references relative to the repository root.
- Stats card generation uses `${{ secrets.GITHUB_TOKEN }}` with public repository scope and `include_all_commits=true`.
