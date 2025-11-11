# notebooks-security Workspace Guide

## Purpose

This repository is the dedicated workspace for **crud-web-apps component upgrades** as part of Kubeflow Notebooks v1.11 Security Hardening (Epic [#720](https://github.com/kubeflow/notebooks/issues/720)).

**Scope of this Fork:**
- ✅ Node.js 24 upgrade for crud-web-apps (frontend)
- ✅ Python 3.12 upgrade for crud-web-apps (backend)
- ✅ All dependencies for crud-web-apps components (Jupyter, Tensorboards, Volumes Web Apps)

**Out of Scope (Handled Directly Upstream):**
- ❌ Go controller upgrades (notebook-controller, tensorboard-controller, pvcviewer-controller)
- ❌ Example notebook servers
- ❌ Other independent components

## Repository Structure

```
notebooks-security (this fork - crud-web-apps ONLY)
└── notebooks-v1 (main development branch)
    ├── feature/node-24-crud-webapps (Node.js upgrade)
    ├── feature/python-3.12-crud-webapps (Python upgrade)
    └── feature/* (other crud-web-apps related tasks)
```

### Components in Scope

This fork focuses exclusively on:
```
components/crud-web-apps/
├── common/
│   ├── frontend/          # Shared frontend dependencies (Node.js)
│   └── backend/           # Shared backend dependencies (Python)
├── jupyter/               # Jupyter Web App
│   ├── frontend/          # Node.js
│   └── backend/           # Python
├── tensorboards/          # Tensorboards Web App
│   ├── frontend/          # Node.js
│   └── backend/           # Python
└── volumes/               # Volumes Web App
    ├── frontend/          # Node.js
    └── backend/           # Python
```

### Upgrade Strategy

**Node.js Upgrade (Single Coordinated Task):**
- All frontend components share the same dependency tree through `crud-web-apps/common/frontend`
- **One coordinated upgrade** covers all three web apps
- Branch: `feature/node-24-crud-webapps`

**Python Upgrade (Single Coordinated Task):**
- All backend components share dependencies through `crud-web-apps/common/backend`
- **One coordinated upgrade** covers all three web app backends
- Smaller scope than frontend upgrade
- Branch: `feature/python-3.12-crud-webapps`

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
# Examples based on task type:

# For the Node.js upgrade:
git checkout -b feature/node-24-crud-webapps

# For the Python upgrade:
git checkout -b feature/python-3.12-crud-webapps

# For dependency updates:
git checkout -b feature/deps-update-package-name

# Work on your task...
# Make changes, test locally

# Commit and push
git add .
git commit -m "feat: upgrade crud-webapps frontend to Node 24 (#728)"
git push origin feature/node-24-crud-webapps
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
git branch -d feature/node-24-crud-webapps
git push origin --delete feature/node-24-crud-webapps
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

**crud-web-apps Upgrades:**
- `feature/node-24-crud-webapps` - Node.js 24 upgrade (single coordinated task)
- `feature/python-3.12-crud-webapps` - Python 3.12 upgrade (single coordinated task)
- `feature/deps-*` - Dependency updates for crud-web-apps
- `feature/docker-*` - Docker/CI changes for crud-web-apps
- `fix/*` - Bug fixes
- `test/*` - Testing improvements

**Note:** Go controller upgrades and other components are handled directly in upstream repository, not in this fork.

## Testing Before Merging

Before creating a PR for crud-web-apps, ensure:

### Frontend (Node.js) Tests
```bash
# Test common frontend library (critical - shared by all web apps)
cd components/crud-web-apps/common/frontend/kubeflow-common-lib
npm ci
npm run build
npm test
npm run lint

# Test each web app frontend
cd components/crud-web-apps/jupyter/frontend
npm ci
npm run build
npm test
npm run lint

cd components/crud-web-apps/tensorboards/frontend
npm ci
npm run build
npm test
npm run lint

cd components/crud-web-apps/volumes/frontend
npm ci
npm run build
npm test
npm run lint
```

### Backend (Python) Tests
```bash
# Test common backend (shared by all web apps)
cd components/crud-web-apps/common/backend
pip install -r requirements.txt
python -m flake8 kubeflow/
python -m black --check kubeflow/

# Test each web app backend
cd components/crud-web-apps/jupyter/backend
pip install -r requirements.txt
python -m pytest
python -m pylint app/

cd components/crud-web-apps/tensorboards/backend
pip install -r requirements.txt
python -m pytest

cd components/crud-web-apps/volumes/backend
pip install -r requirements.txt
python -m pytest
```

### Integration Tests (Optional but Recommended)
```bash
# Requires Kind cluster - see components/testing/gh-actions/
./components/testing/gh-actions/install_kind.sh
kind create cluster --config components/testing/gh-actions/kind-1-25.yaml

# Run integration tests for crud-web-apps components
# JWA (Jupyter Web App)
# TWA (Tensorboards Web App)
# VWA (Volumes Web App)
# (workflows will run these automatically on PR)
```

### Docker Build Tests
```bash
# Test Docker builds for each web app
cd components/crud-web-apps/jupyter
make docker-build

cd components/crud-web-apps/tensorboards
make docker-build

cd components/crud-web-apps/volumes
make docker-build
```

## Tasks for This Fork

See [Epic #720](https://github.com/kubeflow/notebooks/issues/720) for the full Epic context.

### crud-web-apps Upgrade Tasks (This Fork)

**Frontend (Node.js) - Single Coordinated Task:**
- [ ] Node.js 24 - `crud-web-apps` upgrade (#728)
  - Upgrades common frontend library: `components/crud-web-apps/common/frontend`
  - Upgrades all dependent web apps:
    - Jupyter Web App frontend
    - Tensorboards Web App frontend
    - Volumes Web App frontend
  - Update all npm dependencies to compatible versions
  - Update Docker base images

**Backend (Python) - Single Coordinated Task:**
- [ ] Python 3.12 - `crud-web-apps` backend upgrade
  - Upgrades common backend: `components/crud-web-apps/common/backend`
  - Upgrades all dependent web apps:
    - Jupyter Web App backend
    - Tensorboards Web App backend
    - Volumes Web App backend
  - Update all pip dependencies to compatible versions
  - Smaller scope than frontend upgrade

**Infrastructure Updates:**
- [ ] Docker base image updates for crud-web-apps
- [ ] CI/CD workflow updates for crud-web-apps

### Tasks Handled Directly Upstream (Not in This Fork)

The following are **NOT** part of this fork and will be handled separately:
- Go 1.24 upgrades for controllers (notebook-controller, tensorboard-controller, pvcviewer-controller)
- Example notebook servers upgrades
- Other independent components

## CI/CD Setup for Fork

### GitHub Actions Workflows

All CI/CD workflows have been adapted for this fork:

**✅ Completed Adaptations:**
- All Docker image registries updated from `ghcr.io/kubeflow` → `ghcr.io/yehudit1987`
- Original configurations preserved as comments with `TODO: Revert to upstream org when merging to kubeflow/notebooks`
- Workflow triggers include `notebooks-v1` branch
- Integration and multi-arch tests configured for fork

### GitHub Container Registry (GHCR) Access

The workflows use `${{ secrets.GITHUB_TOKEN }}` to publish to GHCR. This is automatically available and no additional secrets need to be configured.

**Required Repository Settings:**

1. Go to repository **Settings** → **Actions** → **General**
2. Under "Workflow permissions", ensure:
   - ✅ "Read and write permissions" is selected
   - ✅ "Allow GitHub Actions to create and approve pull requests" is checked

3. Go to repository **Settings** → **Packages**
4. For each published package, ensure the repository has write access

### Viewing Published Images

Published images will be available at:
- `ghcr.io/yehudit1987/notebooks/jupyter-web-app`
- `ghcr.io/yehudit1987/notebooks/notebook-controller`
- `ghcr.io/yehudit1987/notebooks/tensorboard-controller`
- `ghcr.io/yehudit1987/notebooks/pvcviewer-controller`
- `ghcr.io/yehudit1987/notebooks/tensorboards-web-app`
- `ghcr.io/yehudit1987/notebooks/volumes-web-app`
- `ghcr.io/yehudit1987/notebooks/notebook-servers/*`

### Reverting Changes for Upstream PR

When creating the final PR to `kubeflow/notebooks`, you'll need to revert the registry changes:

```bash
# Search for all TODO comments in workflows
grep -r "TODO: Revert to upstream org" .github/workflows/

# Uncomment the original lines and remove the fork-specific lines
# This can be done in bulk or file-by-file before the upstream PR
```

All workflow files have clear `TODO` markers indicating what needs to be reverted.

## Important Notes

⚠️ **This is NOT the official Kubeflow repository** - it's our team workspace for crud-web-apps only.

⚠️ **This fork is ONLY for crud-web-apps components** - Go controllers and other components are handled directly upstream.

✅ **Always work from `notebooks-v1` branch**, not main.

✅ **Always create PRs against `notebooks-v1`**, not upstream.

✅ When ready to merge to upstream, we'll create a single PR from `notebooks-v1` → `kubeflow/notebooks:main` with all crud-web-apps upgrades.

⚠️ **Remember to revert workflow registry changes** before creating the upstream PR.

### Why This Approach?

**crud-web-apps components share dependencies:**
- All three web apps (Jupyter, Tensorboards, Volumes) depend on `crud-web-apps/common/frontend` and `crud-web-apps/common/backend`
- Upgrading Node.js or Python affects all three web apps simultaneously
- Requires coordinated testing across all components
- Better to work in a fork to iterate and test together

**Other components are independent:**
- Go controllers are independent of crud-web-apps
- Can be upgraded directly in upstream without coordination
- Don't need the fork workflow

## Contact

Questions? Reach out to the team lead or ask in the team channel.

---

**Repository**: https://github.com/yehudit1987/notebooks-security  
**Epic**: https://github.com/kubeflow/notebooks/issues/720  
**Upstream**: https://github.com/kubeflow/notebooks

