# GitHub Profile README — Design Spec

**Date:** 2026-06-23  
**Profile:** github.com/TeeThanakit  

---

## Overview

A simple, evergreen GitHub profile README — friendly, minimal, bullet-point driven. The goal is personal brand: feel like a real person, not a template. One dynamic visual element (contribution snake) adds life without requiring any manual upkeep.

---

## Repository Setup

GitHub profile READMEs live in a special repo with the same name as the username. Steps:

1. Create a new public repo named `TeeThanakit` at github.com/TeeThanakit
2. Add a `README.md` at the root — GitHub renders it automatically on the profile page
3. Add a GitHub Action workflow at `.github/workflows/snake.yml` to generate the contribution snake SVG

---

## README Content

### Structure (in order)

```
H1: Hi 👋, I'm Tee
H3: A Software Engineer based in Thailand 🇹🇭

Bullet list:
  🌱 Currently learning Cloud, DevOps and System Design
  🎓 Graduated from KMUTT — BE Computer Engineering, Second Class Honours
  ⚡ Fun fact — I'm always open to new adventures and interesting opportunities
  📫 Reach me via [LinkedIn] and [Email]

Contribution snake (animated SVG embed)
```

### Full README.md

```markdown
<h1 align="center">Hi 👋, I'm Tee</h1>
<h3 align="center">A Software Engineer based in Thailand 🇹🇭</h3>

- 🌱 I'm currently learning **Cloud, DevOps and System Design**
- 🎓 Graduated from **KMUTT** with a BE in Computer Engineering — Second Class Honours
- ⚡ Fun fact — I'm always open to new adventures and interesting opportunities
- 📫 Reach me via [LinkedIn](https://www.linkedin.com/in/thanakit-chokbunsuwan-15259a14b/) or **tchokbunsuwan@gmail.com**

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github.com/TeeThanakit/TeeThanakit/blob/output/github-contribution-grid-snake-dark.svg" />
  <source media="(prefers-color-scheme: light)" srcset="https://github.com/TeeThanakit/TeeThanakit/blob/output/github-contribution-grid-snake.svg" />
  <img alt="contribution snake" src="https://github.com/TeeThanakit/TeeThanakit/blob/output/github-contribution-grid-snake.svg" />
</picture>
```

> **Design decisions:**
> - Employer name omitted intentionally — avoids stale content when changing jobs
> - `⚡ Fun fact` line doubles as an open-to-work signal without being explicit
> - Snake uses `<picture>` to serve a dark variant on dark-mode GitHub

---

## Contribution Snake

The snake is an animated SVG generated from the GitHub contribution graph. It is committed to an `output` branch by a scheduled GitHub Action.

### GitHub Action — `.github/workflows/snake.yml`

```yaml
name: Generate Contribution Snake

on:
  schedule:
    - cron: "0 0 * * *"   # runs daily at midnight UTC
  workflow_dispatch:        # allows manual trigger

jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
      - uses: Platane/snk/svg-only@v3
        with:
          github_user_name: TeeThanakit
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?palette=github-dark

      - uses: crazy-max/ghaction-github-pages@v3
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### How it works

1. The action runs daily (and on manual trigger)
2. `Platane/snk` reads the public contribution graph for `TeeThanakit` and generates two SVGs — light and dark variants
3. The SVGs are pushed to the `output` branch
4. The README embeds them via raw GitHub URLs — no CDN or third-party service involved

### First-run note

On the first push, the `output` branch won't exist yet. Trigger the action manually from the Actions tab (`workflow_dispatch`) to generate the SVGs before the README goes live.

The default `GITHUB_TOKEN` has write access on public repos, so no extra secret configuration is needed. If the action fails with a permissions error, go to Settings → Actions → General → Workflow permissions and enable "Read and write permissions".

---

## File Structure

```
TeeThanakit/          ← repo root (same name as GitHub username)
├── README.md
└── .github/
    └── workflows/
        └── snake.yml
```

---

## Out of Scope

- GitHub stats cards (not selected)
- Top languages card (not selected)
- Tech stack badges (not selected)
- Profile views counter (not selected)
