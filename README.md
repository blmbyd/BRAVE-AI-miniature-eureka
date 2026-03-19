# BRAVE-AI-miniature-eureka
GitHub Agentic Workflows Demo

A hands-on demo repository for [GitHub Agentic Workflows (gh-aw)](https://github.github.com/gh-aw/) — the new feature that lets you write GitHub Actions in plain Markdown using natural language instructions instead of YAML.

## What are GitHub Agentic Workflows?

GitHub Agentic Workflows allow you to define automation tasks for your repository using Markdown files (`.md`) instead of traditional YAML-based GitHub Actions. You describe your **intent** in natural language, and an AI agent figures out how to accomplish the task using the allowed tools and safe-output guardrails.

Each workflow file lives in `.github/workflows/` and consists of:
- **YAML frontmatter** — declares the trigger (`on`), permissions, tools, and safe-output constraints
- **Markdown body** — natural language instructions describing what the agent should do

The Markdown source is compiled to a `.lock.yml` file using the `gh aw compile` command, which produces a hardened, SHA-pinned GitHub Actions workflow.

## Demo Workflows

This repository contains example agentic workflows that demonstrate several trigger and reporting patterns:

### 1. 🏷️ Issue Triage ([`issue-triage.md`](.github/workflows/issue-triage.md))

**Trigger:** Automatically runs whenever a new issue is opened or reopened.

Analyzes each incoming issue, selects appropriate labels from the repository's label list, checks for duplicates, and posts a structured triage comment with debugging strategies and recommended next steps.

### 2. 📊 Daily Repo Status ([`daily-repo-status.md`](.github/workflows/daily-repo-status.md))

**Trigger:** Runs on a daily schedule (or manually via `workflow_dispatch`).

Collects the past 24 hours of repository activity — issues, pull requests, releases, and community highlights — and creates a GitHub issue with an upbeat team status report.

### 3. 🔍 PR Reviewer ([`pr-reviewer.md`](.github/workflows/pr-reviewer.md))

**Trigger:** Automatically runs when a pull request is opened or updated.

Reviews the PR diff for correctness, clarity, security concerns, and convention adherence, then posts a constructive review comment with specific file and line citations.

### 4. Daily Repo Status — Discussions ([`daily-repo-status-discussions.md`](.github/workflows/daily-repo-status-discussions.md))

**Trigger:** Runs on a daily schedule (or manually via `workflow_dispatch`).

Same activity collection and report structure as the issue-based daily status workflow, but publishes the report as a GitHub Discussion in the `announcements` category instead of creating an issue. Older daily discussions are automatically closed when a new one is posted. Fallback to issue creation is disabled, so failures surface explicitly.

### 5. Weekly Issue Status — Discussions ([`weekly-issue-status-discussions.md`](.github/workflows/weekly-issue-status-discussions.md))

**Trigger:** Runs weekly on Monday mornings around 06:00 UTC (or manually via `workflow_dispatch`).

Collects only issue activity from the past 7 days and publishes a GitHub Discussion with two views: updates per users and updates per project hierarchy. Project rollups start from issues whose type is `Project` and recursively include all linked child issues and sub-issues.

### 6. 🚨 CI/CD Failure Report ([`cicd-failure-report.md`](.github/workflows/cicd-failure-report.md))

**Trigger:** Automatically runs whenever the `.NET CI` workflow completes with a `failure` conclusion.

Detects failed `.NET CI` workflow runs, collects job and step failure details, and creates a structured GitHub issue assigned to Copilot so the failure can be investigated and resolved — potentially automatically — without manual intervention. Runs that succeed, are cancelled, or time out produce no output.

## Quick Start

### Prerequisites

- [GitHub CLI](https://cli.github.com/) installed and authenticated
- `gh-aw` CLI extension installed:

  ```bash
  gh extension install github/gh-aw
  ```

- Access to GitHub Agentic Workflows (currently in technical preview — sign up at <https://github.github.com/gh-aw/> — if the link returns a 404, the preview may not yet be available in your region; check the [GitHub Changelog](https://github.blog/changelog/) for the latest availability updates)
- An AI model configured (GitHub Copilot, Anthropic Claude, or another supported provider)
- **For `cicd-failure-report.md` only:** A fine-grained personal access token stored as the `GH_AW_AGENT_TOKEN` repository secret with `actions: read`, `contents: write`, `issues: write`, and `pull-requests: write` scopes. This is required for Copilot issue assignment; the default `GITHUB_TOKEN` does not have sufficient permissions. The Copilot coding agent must also be enabled for the repository or organization.

### Setup

1. **Clone this repository** and navigate into it:

   ```bash
   git clone https://github.com/blmbyd/BRAVE-AI-miniature-eureka.git
   cd BRAVE-AI-miniature-eureka
   ```

2. **Compile the workflows** to generate the required `.lock.yml` files:

   ```bash
   gh aw compile .github/workflows/issue-triage.md
   gh aw compile .github/workflows/daily-repo-status.md
   gh aw compile .github/workflows/daily-repo-status-discussions.md
   gh aw compile .github/workflows/weekly-issue-status-discussions.md
   gh aw compile .github/workflows/pr-reviewer.md
   gh aw compile .github/workflows/cicd-failure-report.md
   ```

   > Note: `daily-repo-status-discussions.md` and `weekly-issue-status-discussions.md` require GitHub Discussions to be enabled on the repository and an announcement-capable `announcements` category to exist.
   >
   > Note: `cicd-failure-report.md` will only trigger when a workflow named exactly `.NET CI` is present on the repository's default branch. If your CI workflow has a different name, update the `workflows:` list in the frontmatter before compiling.

3. **Commit and push** the generated `.lock.yml` files:

   ```bash
   git add .github/workflows/*.lock.yml
   git commit -m "Add compiled agentic workflow lock files"
   git push
   ```

4. The workflows are now active. Open an issue or create a pull request to see the agents in action!

### Running a workflow manually

```bash
# Trigger the issue-based daily status report right now
gh aw run daily-repo-status

# Trigger the discussion-based daily status report right now
gh aw run daily-repo-status-discussions

# Trigger the weekly issue discussion report right now
gh aw run weekly-issue-status-discussions
```

## Learn More

- 📖 [GitHub Agentic Workflows documentation](https://github.github.com/gh-aw/)
- 🚀 [Quick Start Guide](https://github.github.com/gh-aw/setup/quick-start/)
- 💡 [Example workflows collection](https://github.com/githubnext/agentics)
