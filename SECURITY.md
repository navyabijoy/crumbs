# Security policy

crumbs reads untrusted text (issue bodies, comments and repository files) and turns it into a local web page, so injection bugs matter.

## Reporting a vulnerability

Please report security problems privately using GitHub's **Report a vulnerability** button on this repository's Security tab. Don't open a public issue.

Useful things to include: what you did, the `trail.json` or issue text that triggers it, and what happened.

## In scope

- Script or HTML injection into the generated page from `trail.json` content
- Links that can run code or leave the `http(s)` scheme
- `render.py` reading or serving files outside the intended folders
- Ways for issue or repo content to make the skill run commands, push, comment, or open PRs

## Supported versions

Only the latest version on `main` is supported.
