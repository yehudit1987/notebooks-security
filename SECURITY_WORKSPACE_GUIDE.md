# notebooks-security Workspace Guide

## Purpose

This repository is the dedicated workspace for **Kubeflow Notebooks v1.11 Security Hardening** (Epic [#720](https://github.com/kubeflow/notebooks/issues/720)).

All team members will collaborate here on vulnerability remediation and toolchain upgrades before merging back to upstream.

## Repository Structure

```
notebooks-security (this repo)
└── notebooks-v1 (main development branch)
    ├── feature/node-24-* (Node.js upgrade branches)
    ├── feature/go-1.24-* (Go upgrade branches)
    ├── feature/python-3.12-* (Python upgrade branches)
    └── feature/* (other security tasks)
```

## Setup for Team Members

### Initial Clone

```bash
cd ~/Projects  # or your preferred directory
git clone https://github.com/yehudit1987/notebooks-security.git
cd notebooks-security
```

### Configure Remotes

```bash
# Add upstream (official Kubeflow notebooks)
git remote add upstream https://github.com/kubeflow/notebooks.git

# Verify remotes
git remote -v
# Should show:
#   origin    https://github.com/yehudit1987/notebooks-security.git
#   upstream  https://github.com/kubeflow/notebooks.git

# Fetch upstream
git fetch upstream
```

### Check Out notebooks-v1

```bash
git checkout notebooks-v1
git pull origin notebooks-v1
```

## Daily Workflow

### Starting a New Task

```bash
# Update your local notebooks-v1
git checkout notebooks-v1
git pull origin notebooks-v1

# Create a feature branch from notebooks-v1
git checkout -b feature/node-24-common-frontend

# Work on your task...
# Make changes, test locally

# Commit and push
git add .
git commit -m "Upgrade common-frontend to Node 24 (#728)"
git push origin feature/node-24-common-frontend
```

### Creating a Pull Request

1. Go to: https://github.com/yehudit1987/notebooks-security
2. Click **"Compare & pull request"**
3. **Base branch**: `notebooks-v1` ⚠️ (Important!)
4. **Compare branch**: your feature branch
5. Add description, link to issue
6. Request team member review
7. After approval, merge to `notebooks-v1`

### After Your PR is Merged

```bash
# Update your local notebooks-v1
git checkout notebooks-v1
git pull origin notebooks-v1

# Delete your feature branch (optional)
git branch -d feature/node-24-common-frontend
git push origin --delete feature/node-24-common-frontend
```

## Keeping in Sync with Upstream

**Maintainer responsibility**: A designated team member should sync weekly.

```bash
# Sync notebooks-v1 with upstream
git checkout notebooks-v1
git fetch upstream

# Merge upstream changes
git merge upstream/notebooks-v1

# Resolve any conflicts, then push
git push origin notebooks-v1
```

**After sync notification**: All team members update their branches:

```bash
git checkout notebooks-v1
git pull origin notebooks-v1

# Update your feature branch
git checkout feature/your-branch
git rebase notebooks-v1

# If conflicts, resolve and continue
git rebase --continue

# Force push (safely)
git push --force-with-lease origin feature/your-branch
```

## Branch Naming Conventions

Use these prefixes for consistency:

- `feature/node-24-*` - Node.js 24 upgrades
- `feature/go-1.24-*` - Go 1.24 upgrades
- `feature/python-3.12-*` - Python 3.12 upgrades
- `feature/deps-*` - Dependency updates
- `feature/docker-*` - Docker/CI changes
- `fix/*` - Bug fixes
- `test/*` - Testing improvements

## Testing Before Merging

Before creating a PR, ensure:

```bash
# Run relevant tests locally
cd components/crud-web-apps/common/frontend/kubeflow-common-lib
npm ci
npm run build
npm test

# Check linting
npm run lint
```

## Current Epic Tasks

See [Epic #720](https://github.com/kubeflow/notebooks/issues/720) for the full list.

**Priority tasks:**
- [ ] Node 24 - common-frontend (#728) - @yehudit1987
- [ ] Node 24 - jupyter-frontend
- [ ] Go 1.24 - controllers
- [ ] Python 3.12 - backends
- [ ] Docker base image updates

## Important Notes

⚠️ **This is NOT the official Kubeflow repository** - it's our team workspace.

✅ **Always work from `notebooks-v1` branch**, not main.

✅ **Always create PRs against `notebooks-v1`**, not upstream.

✅ When ready to merge to upstream, we'll create a single large PR from `notebooks-v1` → `kubeflow/notebooks:main`.

## Contact

Questions? Reach out to the team lead or ask in the team channel.

---

**Repository**: https://github.com/yehudit1987/notebooks-security  
**Epic**: https://github.com/kubeflow/notebooks/issues/720  
**Upstream**: https://github.com/kubeflow/notebooks

