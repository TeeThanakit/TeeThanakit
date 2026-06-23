# GitHub Profile README Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use subagent-driven-development (recommended) or build to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the TeeThanakit GitHub profile repo with a README and a daily GitHub Action that generates a contribution snake SVG.

**Architecture:** Two files at the repo root — `README.md` with static content and a `<picture>` embed for the snake, and `.github/workflows/snake.yml` which runs `Platane/snk` on a daily cron and pushes the generated SVGs to an `output` branch. The README URLs point at that branch.

**Tech Stack:** Markdown, GitHub Actions, Platane/snk@v3, crazy-max/ghaction-github-pages@v3

---

## File Map

| File | Action | Purpose |
|---|---|---|
| `README.md` | Create | Profile content — greeting, bullets, snake embed |
| `.github/workflows/snake.yml` | Create | Daily Action to generate snake SVGs into `output` branch |

---

### Task 1: Initialise the repo and create README.md

**Files:**
- Create: `README.md`

- [ ] **Step 1: Initialise git**

```bash
cd /Users/teeboy/repos/TeeThanakit
git init
git checkout -b main
```

Expected: `Initialized empty Git repository in .../TeeThanakit/.git/`

- [ ] **Step 2: Create README.md**

Create `/Users/teeboy/repos/TeeThanakit/README.md` with this exact content:

```markdown
<h1 align="center">Hi 👋, I'm Tee</h1>
<h3 align="center">A Software Engineer based in Thailand 🇹🇭</h3>

- 🌱 I'm currently learning **Cloud, DevOps and System Design**
- 🎓 Graduated from **KMUTT** with a BE in Computer Engineering — Second Class Honours
- ⚡ Fun fact — I'm always open to new adventures and interesting opportunities
- 📫 Reach me via [LinkedIn](https://www.linkedin.com/in/thanakit-chokbunsuwan-15259a14b/) or **tchokbunsuwan@gmail.com**

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/TeeThanakit/TeeThanakit/output/github-contribution-grid-snake-dark.svg" />
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/TeeThanakit/TeeThanakit/output/github-contribution-grid-snake.svg" />
  <img alt="contribution snake" src="https://raw.githubusercontent.com/TeeThanakit/TeeThanakit/output/github-contribution-grid-snake.svg" />
</picture>
```

> Note: URLs use `raw.githubusercontent.com` — the `github.com/blob/` URLs render an HTML page, not the raw SVG, so the image would break.

- [ ] **Step 3: Verify the file looks right**

```bash
cat /Users/teeboy/repos/TeeThanakit/README.md
```

Expected: The full markdown content above, no truncation.

- [ ] **Step 4: Commit**

```bash
git add README.md
git commit -m "feat: add profile README"
```

---

### Task 2: Add the contribution snake GitHub Action

**Files:**
- Create: `.github/workflows/snake.yml`

- [ ] **Step 1: Create the workflows directory**

```bash
mkdir -p /Users/teeboy/repos/TeeThanakit/.github/workflows
```

- [ ] **Step 2: Create snake.yml**

Create `/Users/teeboy/repos/TeeThanakit/.github/workflows/snake.yml` with this exact content:

```yaml
name: Generate Contribution Snake

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

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

- [ ] **Step 3: Commit**

```bash
git add .github/workflows/snake.yml
git commit -m "feat: add contribution snake GitHub Action"
```

---

### Task 3: Create the GitHub repo and push

- [ ] **Step 1: Create the public repo on GitHub**

```bash
gh repo create TeeThanakit --public --description "GitHub profile README"
```

Expected output includes: `✓ Created repository TeeThanakit/TeeThanakit on GitHub`

- [ ] **Step 2: Add remote and push**

```bash
git remote add origin https://github.com/TeeThanakit/TeeThanakit.git
git push -u origin main
```

Expected: Branch `main` pushed, upstream set.

- [ ] **Step 3: Verify on GitHub**

Open `https://github.com/TeeThanakit` in a browser. The README should render immediately on the profile page.

---

### Task 4: Trigger the snake Action and verify

- [ ] **Step 1: Enable Actions write permissions (if needed)**

Go to `https://github.com/TeeThanakit/TeeThanakit/settings/actions` → General → Workflow permissions → select **Read and write permissions** → Save.

- [ ] **Step 2: Trigger the Action manually**

```bash
gh workflow run snake.yml --repo TeeThanakit/TeeThanakit
```

- [ ] **Step 3: Watch it complete**

```bash
gh run watch --repo TeeThanakit/TeeThanakit
```

Expected: Run completes with status `✓ completed`. This usually takes under 30 seconds.

- [ ] **Step 4: Verify the output branch exists**

```bash
gh api repos/TeeThanakit/TeeThanakit/branches --jq '.[].name'
```

Expected: `main` and `output` both listed.

- [ ] **Step 5: Verify the snake appears on the profile**

Open `https://github.com/TeeThanakit` in a browser. The animated snake should now be visible below the bullet points.

If it shows a broken image, wait 30 seconds and hard-refresh — GitHub CDN can take a moment to pick up new raw file URLs.
