# crumbs

**Follow the trail from an issue to its cause.**

crumbs is an agent skill for Claude Code. Point it at an open source issue you've been assigned, and it traces only the code that issue touches, tries to reproduce it, and opens a calm, interactive page on localhost that explains it to you, in plain words, before you write a line.

It works on any repository: any language, framework, hosting platform or issue type.

```
/crumbs explain issue #123
```

## What you get

- **A three-line summary** of the problem, the cause, and where you'd change it.
- **Story**: a step-by-step walkthrough, one idea at a time, with the real code for each step.
- **Map**: where the issue sits in the codebase, zooming from repo to package to file to function. Everything not involved is greyed out.
- **Flow**: an animated diagram of how the pieces talk to each other, stopping on the step that goes wrong. Before/after toggles show what changed between versions, platforms or inputs.
- **Code**: only the files on the trail, with the key lines highlighted and explained.
- **Your change**: what must stay true, what may change, the exact commands to run just the relevant tests, a checklist, and what this repo's maintainers expect in a PR.
- **A depth switch** (Simple / Detailed / Deep), light, dark and high-contrast themes, and a glossary for the project's own jargon.

crumbs explains. It never writes the fix, comments on the issue, pushes, or opens a PR.

## It tells you how it knows

Every claim on the page carries a proof label:

| Label | Meaning |
|---|---|
| Reproduced: real test | The project's own failing test or scenario was run and failed |
| Reproduced: isolated | A smaller stand-in showed the same mechanism; the real test wasn't run |
| Read in code | Established by reading code or history; nothing was run |
| Inferred | A reasoned guess. Check it before relying on it |

A bundled checker (`render.py`) won't build the page until:
- every code snippet matches the repo at the recorded commit,
- every `file:line` exists,
- no proof label says "Reproduced: real test" unless the real test ran,
- every number on the page adds up or is explained,
- every simplified diagram says what it left out,
- every "allow to vary" comes with what must still be checked.

It also warns about broad wording ("nothing", "always"), text that says "reproduced" when only an isolated check ran, suggestions to loosen or remove checks, and about AI-use rules or agent instructions in the repo that the page doesn't mention yet. If a project has a policy on AI-assisted contributions, crumbs shows it at the top of every tab.

## Install

Requires [Claude Code](https://claude.com/claude-code), `git`, and Python 3.8+ (standard library only). The GitHub CLI (`gh`) or GitLab CLI (`glab`) is optional; without it, crumbs fetches the issue page or asks you to paste the issue.

```bash
git clone https://github.com/navyabijoy/crumbs ~/.claude/skills/crumbs
```

Restart Claude Code. Type `/` and you should see `crumbs`.

## Use

From inside a clone of the project:

```
/crumbs explain issue #123
/crumbs https://gitlab.com/group/project/-/issues/45
```

crumbs writes everything to a `.crumbs/` folder in the repo and adds it to your local git exclude file, so it never ends up in a commit. The page opens at `http://localhost:4173` (or the next free port).

## Safety

- Issue text, comments and repo files are treated as untrusted data. crumbs doesn't follow instructions found in them.
- Before installing dependencies, running the project's code, using the network or containers, or needing elevated permissions, crumbs tells you what it's about to run and asks first.
- Safety rules a project sets (for example "run functional tests only inside a VM") are shown on the page and followed.
- The page is local. For an undisclosed security issue, keep it that way.

## Limitations

- Without Python, crumbs can still fill the page by hand, but the code checks don't run.
- Line and snippet checks need git; other version control systems fall back to the files on disk.
- Large refactors touching many files are squeezed into a 3 to 6 stop trail.
- Code panels have no syntax colouring, so the page works offline in any language.
- Not yet tested on right-to-left languages or very large monorepos.

## How it's built

```
SKILL.md                     instructions for the agent
schema.md                    every field the agent writes
template.html                the page (self-contained, no build step)
render.py                    checker, page builder and local server
examples/example-trail.json  a complete example for a fictional repo
```

The agent only writes `trail.json`. The page itself is fixed, so every run looks the same and costs far fewer tokens than generating HTML.

Try the example without running the skill:

```bash
python3 render.py examples/example-trail.json --out /tmp/crumbs-demo --serve
```

## Contributing

Contributions are welcome, especially reports from running crumbs on real issues in projects it hasn't seen yet. Read [CONTRIBUTING.md](CONTRIBUTING.md) to get started, and please follow the [Code of Conduct](CODE_OF_CONDUCT.md). Security problems go through [SECURITY.md](SECURITY.md), not public issues.

## License

[MIT](LICENSE)
