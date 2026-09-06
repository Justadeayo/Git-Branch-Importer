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

---

## ✨ Features

* 🚀 **Zero Local Setup:** Trigger operations directly from the GitHub UI via `workflow_dispatch`.
* 🔒 **Centralized Authentication:** Uses a single encrypted repository secret (`GLOBAL_REPO_PAT`) to grant push access across all target repositories.
* 🍒 **Granular Commit Cherry-Picking:** Sync specific fixes or features from upstream repos without merging entire branches.
* 👤 **Customizable Committer Identity:** Configure your own Git name and email so cherry-picked commits link directly to your GitHub contribution profile.
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

### 3. Customize Your Committer Identity (Optional but Recommended)
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
## 📑 Version History

| Version | Date | Notes | Branch |
| :--- | :--- | :--- | :--- |
| **1.0** | 2026-09-07 | Initial release with `import-branch` and `apply-upstream-commit` workflows | `main` |

---
**Last Updated:** 2026-09-07  
**Primary Branches:** main  
**Status:** 🚀 Active Toolset
---
*Maintained by [Justus](https://github.com/Justadeayo) for clean repository management and branch synchronization.*