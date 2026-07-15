---
name: pick-issue
description: Lists open GitHub issues for a repository and lets the user pick one for Claude to implement. Use when the user says "pick an issue", "list the issues", "show me the GitHub issues", "what issues are open", "work on an issue", or invokes /pick-issue. Accepts an optional repository argument (owner/repo or GitHub URL); defaults to the current repository's origin remote.
---

# Pick a GitHub Issue and Implement It

Fetch the open issues of a GitHub repository with the `gh` CLI, present them to the user, let them choose one, then implement it in the current working directory.

## Step 1: Resolve the repository

- If an argument was given, use it. Accept `owner/repo` or a full GitHub URL (strip it down to `owner/repo`).
- Otherwise infer it from the current directory: `git remote get-url origin`. Convert the remote URL to `owner/repo`.
- If neither works (no argument, no git repo/remote), ask the user which repository to use and stop until they answer.

## Step 2: Verify access

Run `gh auth status` once. If it fails, tell the user to run `gh auth login` in a terminal and stop — never ask for or handle tokens yourself.

## Step 3: List the open issues

```bash
gh issue list --repo <owner/repo> --state open --limit 30 --json number,title,labels,assignees,updatedAt
```

- If there are no open issues, say so and stop.
- Present the issues as a compact markdown table: number, title, labels, assignee. Order as returned (most recently updated first).
- If more than 30 exist, mention that only the 30 most recent are shown and that the user can ask for more or filter by label.

## Step 4: Let the user choose

- If there are 4 or fewer issues, use the AskUserQuestion tool with one option per issue (label: `#<number> <short title>`; description: labels + one-line gist).
- Otherwise, show the table and ask the user to reply with the issue number they want implemented. Do not pick one yourself.

## Step 5: Understand the chosen issue

```bash
gh issue view <number> --repo <owner/repo> --json number,title,body,labels,comments,url
```

Read the body and comments. Summarize in 2–3 sentences what needs to be done and what "done" looks like. If the issue is ambiguous or lacks acceptance criteria, ask the user the minimum clarifying questions before writing code.

## Step 6: Implement

- Explore the relevant code first; follow the project's existing conventions and CLAUDE.md instructions.
- Make the smallest change that resolves the issue. Run the project's build/tests if available and report results honestly.
- When done, summarize the change and offer next steps: commit on a branch, and open a PR whose description references the issue (`Fixes #<number>`). Only commit, push, or open the PR if the user confirms.

## Rules

- All GitHub access goes through the `gh` CLI — no raw API calls, no token handling.
- Never close, edit, or comment on the issue itself unless the user explicitly asks.
