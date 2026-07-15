# github-issue-picker

Claude Code plugin that lists a GitHub repository's open issues, lets you pick one, and has Claude implement it.

## Requirements

- [GitHub CLI](https://cli.github.com) (`gh`) installed and authenticated (`gh auth login`).

## Usage

```
/pick-issue                # uses the current repo's origin remote
/pick-issue owner/repo     # or any GitHub repo you have access to
```

Claude fetches the open issues, shows them as a list, and waits for you to choose. After you pick one, it reads the full issue (body + comments), implements the change in your working directory, and offers to commit and open a PR referencing the issue.

Natural-language triggers also work: "show me the open issues", "pick an issue to work on".

## What it never does

- Handle GitHub tokens (auth is delegated entirely to `gh`).
- Commit, push, or open a PR without your confirmation.
- Close or comment on issues.
