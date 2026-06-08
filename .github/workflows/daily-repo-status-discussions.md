---
description: |
  Generates a daily status report for the repository as a GitHub Discussion.
  Collects recent activity (issues, pull requests, discussions, code changes,
  releases) and produces an upbeat summary with progress highlights,
  community shout-outs, and actionable recommendations for maintainers.

on:
  schedule: daily
  workflow_dispatch:

permissions:
  contents: read
  issues: read
  pull-requests: read
  discussions: read

strict: true

network: defaults

tools:
  github:
    toolsets: [issues, pull_requests, repos, discussions]

safe-outputs:
  create-discussion:
    title-prefix: "[daily-status] "
    category: announcements
    labels: [report, daily-status]
    close-older-discussions: true
    fallback-to-issue: false

timeout-minutes: 15
---

# Daily Repo Status

Create an upbeat and informative daily status report for `${{ github.repository }}` and publish it as a GitHub Discussion in the announcements category.

## What to collect

Use the available GitHub tools to gather the following information for the past 24 hours:

- **Issues**: newly opened, closed, and commented-on issues
- **Pull requests**: opened, merged, and reviewed pull requests
- **Discussions**: new discussions or active threads (if available)
- **Releases**: any new releases or tags published
- **Code activity**: notable commits or branch activity

## Report format

Structure the report as a GitHub Discussion with these sections:

### Summary
A two-to-three sentence overview of the day's activity. Positive and encouraging in tone.

### Issues and Pull Requests
- New issues opened (count + brief highlights)
- Issues closed / resolved
- PRs opened, merged, or awaiting review

### Community Highlights
Call out notable contributions, first-time contributors, or particularly helpful discussions.

### Project Health
- Open issue count trend
- PR review queue size
- Any stale items that might need attention

### Recommended Next Steps
Two or three concrete, actionable suggestions for maintainers based on current activity.

## Style guidelines

- Be positive, encouraging, and concise
- Use plain text formatting for readability
- Adjust report length to match actual activity -- a quiet day deserves a short report
- Do not @mention individual users
- Always publish the discussion even on quiet days; a brief summary is better than no output
