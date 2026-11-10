# Contributing to crumbs

Thanks for helping. crumbs exists to make open source issues easier to start on, so this project tries to be easy to start on too.

## Ways to help

- **Try it on a real issue** and open an issue with what went wrong or felt confusing. Include the repo, issue link, language, host (GitHub, GitLab, ...) and which agent you used (Claude Code, Codex CLI, opencode, Antigravity, ...). This is the most useful thing you can do: crumbs has to work on any project, and every new kind of repo finds new edge cases.
- **Fix a bug or improve the page.** Issues labelled `good first issue` are a good place to begin.
- **Improve the docs**: `README.md`, `SKILL.md` or `references/schema.md`.

If you're planning something bigger than a small fix, open an issue first so we can agree on the approach before you spend time on it.

## How the project fits together

| File | What it does |
|---|---|
| `SKILL.md` | Instructions the agent follows to investigate an issue and write `trail.json` |
| `references/schema.md` | Every field of `trail.json` and its limits |
| `scripts/render.py` | Checks `trail.json` against the repo, fills the template, serves the page. Python 3.8+, standard library only |
| `assets/template.html` | The page. One self-contained file: no build step, no framework, no required network |
| `examples/example-trail.json` | A complete example for a fictional repo |

## Setting up

You need Python 3.8+ and a browser. Nothing to install.

```bash
git clone https://github.com/navyabijoy/crumbs
cd crumbs
python3 scripts/render.py examples/example-trail.json --out /tmp/crumbs-demo --serve
```

To try your changes as a skill, link your clone into your agent's skills folder (see the README's Install section for each agent), for example:

```bash
ln -s "$(pwd)" ~/.claude/skills/crumbs        # Claude Code
ln -s "$(pwd)" ~/.agents/skills/crumbs        # Codex CLI, Antigravity and others
```

## Ground rules for changes

These keep crumbs useful on any project and honest about what it knows.

- **Stay repo-agnostic.** Nothing in `SKILL.md`, `references/schema.md`, `scripts/render.py` or `assets/template.html` may assume a language, framework, host, project or coding agent. Examples must be clearly fictional.
- **Keep `scripts/render.py` dependency-free** and compatible with Python 3.8.
- **Keep `assets/template.html` self-contained.** It must work opened straight from disk, offline, in light, dark and high-contrast themes, on a phone-sized screen, and with `prefers-reduced-motion`. Escape everything that comes from `trail.json`, and only ever turn `http(s)` URLs into links.
- **Never weaken the honesty checks.** Proof labels, number explanations, snippet matching and the "keep checking" rule exist so readers can trust the page. Changes that make them stricter or clearer are welcome; changes that make them easier to bypass are not.
- **crumbs only reads and explains.** It must never push, comment on issues or open PRs on the user's behalf.
- **If you add a field** to `trail.json`, update `references/schema.md`, the validator in `scripts/render.py`, the template, and the example together.

## Before you open a pull request

```bash
python3 -m py_compile scripts/render.py
python3 scripts/render.py examples/example-trail.json --check-only
```

Both must pass (CI runs them on Python 3.8 and 3.12). If you changed the page, open the example in a browser, click through all five tabs, switch depth and theme, and include a screenshot in the PR.

Keep PRs focused on one change, and describe what changed and why in your own words. Reference the issue it addresses.

## Using AI tools

AI-assisted contributions are welcome. This project is an AI skill, after all. We ask that you:

- **Understand and verify everything you submit.** You should be able to explain every line, and you should have run the checks above yourself.
- **Say so in the PR description** if an AI tool wrote a meaningful part of the change.
- **Write PR descriptions and review replies yourself.**

Large changes that look generated and unchecked will be closed.

## Reporting security issues

Please don't open a public issue for a security problem (for example, a way for issue text to inject script into the page). Use GitHub's **Report a vulnerability** button on the repository's Security tab instead. See `SECURITY.md`.

## Code of conduct

Everyone taking part is expected to follow the [Code of Conduct](CODE_OF_CONDUCT.md).

By contributing, you agree that your contributions are licensed under the [MIT License](LICENSE).
