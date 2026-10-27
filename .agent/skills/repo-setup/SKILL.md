---
name: repo-setup
version: "1.0.0"
description: "Use when saving or backing up this project, setting up its GitHub remote, or preparing a first deployment."
---

# Repo Setup

## Tone

Every time you use a technical term, immediately explain it in plain language in parentheses, such as "commit (save a snapshot)" or "push (send changes to GitHub)". Assume the user may not be technical.

## Steps

### 1. Ensure this is a git repo

```bash
git rev-parse --git-dir
```

If this is not a git repository (e.g. the user downloaded a zip), initialize one and create an initial commit:

```bash
git init
git add .
git commit -m "Initial commit"
```

### 2. Detect the GitHub host

Read `origin` to identify the GitHub host:

```bash
git remote get-url origin
```

Use the URL hostname, such as `github.com` or an Enterprise host, as `<hostname>` below.

If `origin` is the template (`salesforce-ux/design-system-2-starter-kit`), keep its hostname but create the user's repository in step 7.

If `origin` is unset, ask which GitHub host to use.

### 3. Ensure Homebrew is installed

```bash
which brew
```

If missing, install it:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 4. Ensure `gh` CLI is installed

```bash
which gh
```

If missing:

```bash
brew install gh
```

### 5. Authenticate with GitHub

Check authentication:

```bash
gh auth status --hostname <hostname>
```

If needed, guide the user through login:

```bash
gh auth login
```

When prompted:
1. **Where do you use GitHub?** If `<hostname>` is `github.com`, select **GitHub.com**. Otherwise, select **Other** and enter the hostname.
2. Follow the remaining browser/token prompts.

### 6. Check for an existing remote repo

If `origin` is set and is not the template repo (from step 2), verify the repo is reachable:

```bash
gh repo view --json name --hostname <hostname>
```

If `origin` is not set or the repo does not exist, tell the user no remote repository was found and ask if they'd like to create one.

### 7. Create a repo (if needed)

Use the current directory name as the default repo name. If it is still `design-system-2-starter-kit`, ask the user what they want to name the project instead. Confirm the name with the user before proceeding.

```bash
gh repo create <repo-name> --internal --source=. --hostname <hostname>
```

Create under the user's personal account with **internal** visibility (accessible to org members). Do not ask about organizations.

### 8. Commit and push

Check for uncommitted changes:

```bash
git status
```

If changes are staged or unstaged, help the user add and commit them. Then prepare to push:

```bash
git push -u origin main
```

Ask the user before pushing. If the default branch is not `main`, use whatever branch is current.

### 9. Confirm

Confirm setup and push completion, and provide the repository URL:

```bash
gh repo view --json url --hostname <hostname> --jq '.url'
```
