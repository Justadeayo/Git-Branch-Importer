# 🔀 Git Branch & Commit Importer

**A lightweight, automated GitHub Actions toolset to import branches and apply individual upstream commits across repositories.**

[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)](#)
[![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)](#)

---

> ⚠️ **IMPORTANT: PLEASE FORK THIS REPOSITORY TO USE THIS FEATURE**  
> GitHub Actions workflows run in the context of the repository hosting them. To run these workflows using your own Personal Access Tokens (`GLOBAL_REPO_PAT`) and target repositories, click the **Fork** button at the top right before getting started.

---

## 📌 Overview

**Git Branch & Commit Importer** eliminates manual `git clone`, `remote add`, `cherry-pick`, and `push` operations when synchronizing code across repositories. 

Using isolated runner environments on GitHub Actions, this utility allows you to:
1. **Import Full Branches:** Copy or overwrite an entire branch from a source repository to any target repository.
2. **Apply Upstream Commits:** Cherry-pick specific single commits from an upstream repository directly into a target branch while preserving author attribution and signing with your own committer identity.
3. **Cherry-Pick Multiple Commits:** Apply a list of commits in order, with automatic fallback to a 3-way or partial patch when a plain cherry-pick conflicts.
4. **Revert Multiple Commits:** Revert a list of commits on a target branch with automatic conflict fallback.

---

## ✨ Features

* 🚀 **Zero Local Setup:** Trigger operations directly from the GitHub UI via `workflow_dispatch`.
* 🔒 **Centralized Authentication:** Uses a single encrypted repository secret (`GLOBAL_REPO_PAT`) to grant push access across all target repositories.
* 🍒 **Granular Commit Cherry-Picking:** Sync specific fixes or features from upstream repos without merging entire branches.
* 👤 **Customizable Committer Identity:** Configure your own Git name and email so cherry-picked commits link directly to your GitHub contribution profile.
* ⚡ **Per-Repository Caching:** Every source and target repository gets its own cache, reused by all workflows. Unchanged repos are not fetched again and changed ones only download new commits.
* 🧹 **Clean Execution:** Runs inside isolated ephemeral workspaces without leaving local workspace clutter.

---

## ⚙️ Setup Instructions

### 1. Create a Personal Access Token (PAT)
1. Go to **GitHub Settings > Developer Settings > Personal Access Tokens > Tokens (classic)**.
2. Generate a token with the **`repo`** scope enabled.

### 2. Store the Secret in Your Forked Repository
1. In your forked repository, navigate to **Settings > Secrets and variables > Actions**.
2. Click **New repository secret**.
3. **Name:** `GLOBAL_REPO_PAT`
4. **Value:** Paste your generated Personal Access Token.

### 3. Add the Signing Key (Cherry-Pick & Revert)
In the same **Secrets and variables > Actions** page, add a secret named `SSH_PRIVATE_KEY` containing the private SSH key used to sign commits made by `cherrypick.yml` and `revert.yml`.

### 4. Customize Your Committer Identity (Optional but Recommended)
To ensure cherry-picked commits are properly signed and linked to your GitHub contribution graph, edit the Git configuration values inside `.github/workflows/apply-upstream-commit.yml`:

```yaml
      - name: Configure Git User
        run: |
          git config --global user.name "YOUR_DISPLAY_NAME"
          git config --global user.email "YOUR_GITHUB_NOREPLY_EMAIL"

      - name: Fetch and Cherry-Pick Commit
        env:
          TARGET_PAT: ${{ secrets.GLOBAL_REPO_PAT }}
          GIT_COMMITTER_NAME: "YOUR_DISPLAY_NAME"
          GIT_COMMITTER_EMAIL: "YOUR_GITHUB_NOREPLY_EMAIL"
```
> **Note:** Find your private noreply email under **GitHub Settings > Emails** (e.g., `username@users.noreply.github.com`).
---
## 🚀 Workflows & Usage
### 1. Import Branch Across Repositories
*Imports an entire branch from a source repository into your target repository.*
1. Go to the **Actions** tab > **Import Branch Across Repositories**.
2. Click **Run workflow** and provide the inputs:

| Input Field | Description | Example |
| :--- | :--- | :--- |
| **Source Repository URL** | HTTPS URL of source repository | `https://github.com/user/source-repo.git` |
| **Source Branch Name** | Branch to copy from source | `main` |
| **Target Repository URL** | HTTPS URL of your target repository | `https://github.com/your-username/target-repo.git` |
| **Target Branch Name** | Branch to create/update on target | `imported-feature` |
| **Force Push** | Set to `true` to overwrite existing branch | `false` |

---
### 2. Apply Upstream Commit
*Fetches and cherry-picks a specific commit SHA from an upstream repository onto a target branch.*
1. Go to the **Actions** tab > **Apply Upstream Commit**.
2. Click **Run workflow** and provide the inputs:

| Input Field | Description | Example |
| :--- | :--- | :--- |
| **Source Repository URL** | HTTPS URL of upstream source repo | `https://github.com/upstream/project.git` |
| **Upstream Commit SHA** | Specific SHA hash of the commit to apply | `a1b2c3d4e5f6...` |
| **Target Repository URL** | HTTPS URL of your target repository | `https://github.com/your-username/target-repo.git` |
| **Target Branch Name** | Branch to apply the commit onto | `main` |

---
### 3. Cherry-Pick Multiple Commits
*Fetches a list of commits from a source repository and applies them in order onto a target branch, signed with your SSH key.*
1. Go to the **Actions** tab > **Cherry-Pick Multiple Commits**.
2. Click **Run workflow** and provide the inputs:

| Input Field | Description | Example |
| :--- | :--- | :--- |
| **Source Repository URL** | HTTPS URL of source repo | `https://github.com/upstream/project.git` |
| **Commit SHAs** | Space or newline-separated SHAs, applied in order | `a1b2c3d e4f5a6b` |
| **Target Repository URL** | HTTPS URL of your target repository | `https://github.com/your-username/target-repo.git` |
| **Target Branch Name** | Branch to apply the commits onto | `main` |

---
### 4. Revert Multiple Commits
*Reverts a list of commits on a target branch, signed with your SSH key.*
1. Go to the **Actions** tab > **Revert Multiple Commits**.
2. Click **Run workflow** and provide the inputs:

| Input Field | Description | Example |
| :--- | :--- | :--- |
| **Commit SHAs** | Space or newline-separated SHAs to revert | `a1b2c3d e4f5a6b` |
| **Target Repository URL** | HTTPS URL of your target repository | `https://github.com/your-username/target-repo.git` |
| **Target Branch Name** | Branch to revert the commits on | `main` |

---
## ⚡ Caching
Each repository a workflow reads from (source and target) is kept as a bare git cache using `actions/cache`, so a repeat run does not clone it again.
* **Key:** a hash of the repository URL (case, trailing `/` and `.git` ignored). One cache per repository, shared by every workflow that uses it.
* **Up-to-date check:** before fetching, the workflow compares the remote branch tip (or the requested commit SHA) with what is cached. If they match, nothing is downloaded and no new cache is saved.
* **Incremental update:** if the remote moved on, only the new commits are fetched into the cached copy, and the updated cache is saved. A repository with no cache yet is fetched in full once.
* **Working copy:** each run builds a fresh working repository that borrows objects from the caches, so cherry-picks, reverts and imports do not copy history.
* **Credentials:** repositories are fetched by URL, never stored as remotes, and the run fails before saving if a token is found in the cache.
* **Cleanup:** after a successful run a `Cache_Cleanup` job keeps only the newest cache per repository, then deletes the least recently used `repo-` caches until the total is under `CACHE_LIMIT_GB` (default `8`, set in the job's `env`). It needs repository workflow permissions that allow `actions: write` for that job.
* **Scope:** GitHub caches are only visible to the branch that created them and the default branch.

---
## 📑 Version History

| Version | Date | Notes | Branch |
| :--- | :--- | :--- | :--- |
| **1.1** | 2026-10-10 | Per-repository clone cache for all workflows (skip when unchanged, incremental fetch, size-based cleanup); documented `cherrypick.yml`, `revert.yml` and the `SSH_PRIVATE_KEY` secret | `main` |
| **1.0** | 2026-09-07 | Initial release with `import-branch` and `apply-upstream-commit` workflows | `main` |

---
**Last Updated:** 2026-10-10  
**Primary Branches:** main  
**Status:** 🚀 Active Toolset
---
*Maintained by [Justus](https://github.com/Justadeayo) for clean repository management and branch synchronization.*