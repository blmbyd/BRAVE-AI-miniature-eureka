---
description: |
  Generates a weekly issue-focused status report for the repository as a GitHub Discussion.
  Collects issue activity from the past 7 days and summarizes it in four sections:
  updates per user, updates per project hierarchy, Project Health, and
  Recommended Next Steps.

on:
  schedule: weekly on monday around 06:00 utc
  workflow_dispatch:

permissions:
  contents: read
  issues: read

strict: true

network: defaults

tools:
  github:
    toolsets: [issues]

safe-outputs:
  create-discussion:
    title-prefix: "[weekly-issues] "
    category: announcements
    labels: [report, weekly-status, issues]
    close-older-discussions: true
    fallback-to-issue: false

timeout-minutes: 15
---

# Weekly Issue Status

Create an informative weekly issue status report for `${{ github.repository }}` and publish it as a GitHub Discussion in the announcements category.

Analyze only issue activity from the past 7 days. Do not include or mention pull requests, commits, code changes, releases, branch activity, or unrelated discussion activity.

## What to collect

Use the available GitHub issue tools to gather the following information for the past 7 days:

- Issues opened
- Issues closed
- Issue comments or other meaningful issue-thread updates
- Issue type metadata, especially issues whose type is `Project`
- Parent-child issue links for all levels below each `Project` issue

## Project hierarchy rules

- Treat an issue as a project root only when its issue type is `Project`
- Starting from each project root, recursively walk all linked child issues and sub-issues across every level
- Roll all weekly activity for descendant issues up to the nearest ancestor whose type is `Project`
- Include updates that happened only on child issues even when the root `Project` issue itself was unchanged during the week
- If an issue could appear in more than one hierarchy, report it only once under the nearest `Project` ancestor to avoid duplication
- If no `Project` issue or project hierarchy had updates this week, still publish the discussion and state that the project section had a quiet week

## Report format

Structure the report as a GitHub Discussion with exactly these four top-level sections and no additional top-level sections.

### Updates Per Users

Group weekly issue activity by GitHub user.

For each user with notable activity, summarize:
- Issues they opened this week
- Issues they closed this week
- Issues where they added meaningful comments or moved the discussion forward

Keep each user summary concise and avoid repeating the same issue details more than necessary.

### Updates Per Project

Group weekly issue activity by top-level issues whose type is `Project`.

For each active project hierarchy, summarize:
- The root `Project` issue
- Important child or descendant issues opened this week
- Child or descendant issues closed or resolved this week
- Notable issue-thread updates that changed project progress, blockers, or next steps

Make it clear when activity happened in nested sub-issues rather than on the root project issue itself.

### Project Health

Summarize the overall issue-based health of the repository and its active project hierarchies.

Focus only on signals that come from issues and project issue trees, such as:
- Active versus quiet project hierarchies
- Open blockers or unresolved questions visible in issue threads
- Project trees with many open child issues or little recent movement
- Any issue backlog patterns that need maintainer attention

Do not mention pull requests, commits, releases, CI, or any non-issue signals in this section.

### Recommended Next Steps

Provide two or three concrete maintainer actions based only on the week's issue activity and project hierarchy state.

Recommendations should:
- Be actionable and specific
- Reference issue follow-up needs, project blockers, or neglected project trees
- Stay grounded in issue activity from the past 7 days

## Style guidelines

- Be concise, plain-text, and easy to scan
- Keep the tone constructive and professional
- Do not @mention individual users
- Do not include pull requests, commits, releases, or code-change summaries anywhere in the discussion
- Always publish the discussion, even on a quiet week
