---
name: first-time-deploy
version: "1.0.0"
description: "Use when publishing this project to GitHub Pages or sharing its deployed link."
---

# Deploy to GitHub Pages

For repeat deployments, run `npm run deploy`. For first-time setup, follow the steps below.

## Tone

Every time you use a technical term, immediately explain it in plain language in parentheses, such as "deploy (build and publish the site)" or "configure Pages (choose the branch GitHub serves)". Assume the user may not be technical.

## Prerequisites

First run [repo-setup](../repo-setup/SKILL.md) for prerequisites, authentication, repository creation, and initial push. Reuse its `<hostname>`, detected from `origin`, below.

## Steps

### 1. Deploy (creates `gh-pages` branch)

Build with hash routing and push `dist/` to `gh-pages`:

```bash
npm run deploy
```

Deploy before configuring Pages; the remote `gh-pages` branch must exist.

### 2. Configure GitHub Pages on the repo

Configure Pages:

```bash
gh api repos/{owner}/{repo}/pages \
  --hostname <hostname> \
  --method POST \
  --field source='{"branch":"gh-pages","path":"/"}' \
  --silent 2>/dev/null \
  || gh api repos/{owner}/{repo}/pages \
     --hostname <hostname> \
     --method PUT \
     --field source='{"branch":"gh-pages","path":"/"}'
```

`POST` creates the configuration; `PUT` updates an existing one.

Verify:

```bash
gh api repos/{owner}/{repo}/pages --hostname <hostname> --jq '.source'
```

Expected: `{ "branch": "gh-pages", "path": "/" }`.

### 3. Get the deployed URL

```bash
gh api repos/{owner}/{repo}/pages --hostname <hostname> --jq '.html_url'
```

Share the URL. If it is not available yet, direct the user to the repository's **Settings → Pages**.
