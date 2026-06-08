---
description: |
  Generates a daily status report for a user-specified public GitHub repository
  and creates the report as a GitHub issue in this controller repository.
  Collects the past 24 hours of activity — issues, pull requests, releases, and
  code changes — from the target repository and produces an upbeat summary with
  progress highlights, community shout-outs, and actionable recommendations.

on:
  workflow_dispatch:
    inputs:
      target_repository:
        description: 'Public repository to analyze in owner/repo format (e.g. microsoft/vscode)'
        required: true
        type: string

permissions:
  contents: read
  issues: read
  pull-requests: read

strict: true

network: defaults

tools:
  github:
    toolsets: [issues, pull_requests, repos]

safe-outputs:
  create-issue:
    title-prefix: "[public-repo-status] "
    labels: [report, daily-status, public-repo]
    close-older-issues: false

timeout-minutes: 15
---

# Daily Status Report for Public Repository

Create an upbeat and informative daily status report for the public repository `${{ github.event.inputs.target_repository }}` and publish it as a GitHub issue in this repository (`${{ github.repository }}`).

**Important:** All activity data must be gathered from `${{ github.event.inputs.target_repository }}`. Do not report on `${{ github.repository }}`.

## Issue title

Use this exact title format: `${{ github.event.inputs.target_repository }} — Daily Status`

## What to collect

Use the available GitHub tools to gather the following information from `${{ github.event.inputs.target_repository }}` for the **past 24 hours**:

- **Issues**: newly opened, closed, and commented-on issues
- **Pull requests**: opened, merged, and reviewed pull requests
- **Releases**: any new releases or tags published
- **Code activity**: notable commits or branch activity

## Report format

Structure the report as a GitHub issue with these sections:

### 🌅 Summary

A two-to-three sentence overview of the past 24 hours of activity in `${{ github.event.inputs.target_repository }}`. Positive and encouraging in tone. Begin with the repository name so the context is immediately clear.

### 📋 Issues & Pull Requests

- New issues opened (count + brief highlights)
- Issues closed / resolved
- PRs opened, merged, or awaiting review

### 🌟 Community Highlights

Call out notable contributions, first-time contributors, or particularly active discussion threads.

### 📊 Project Health

- Open issue count trend
- PR review queue size
- Any stale items that might need attention

### 🔜 Recommended Next Steps

Two or three concrete, actionable suggestions for maintainers based on current activity.

## Style guidelines

- Be positive, encouraging, and concise 🌟
- Make it clear throughout the report that the data is from `${{ github.event.inputs.target_repository }}`
- Use emojis moderately for readability
- Adjust report length to match actual activity — a quiet day deserves a short report
- Always create the issue even on quiet days; a brief summary is better than no output
- Do not @mention individual users
