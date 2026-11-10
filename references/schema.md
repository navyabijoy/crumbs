# trail.json field reference

`trail.json` is the only thing written for each issue. `render.py` checks it and puts it into `template.html`. Links (`url`, `issueUrl`, `fileUrlTemplate`) must start with `http://` or `https://`. Any section whose data is missing is hidden on the page, so leave out what doesn't apply. Never invent data to fill a field.

A complete, working example (a fictional repo) is in `examples/example-trail.json`.

## Limits enforced by render.py
- `simple` up to 20 words, `detailed` up to 60, `deep` up to 120
- `deep` is shown **together with** `detailed`, never instead of it, so it must add something new (why the code is built this way, history, edge cases, what to watch for). render.py rejects a `deep` that mostly repeats `detailed`
- `plainTitle` and each `tldr` field up to 12 words
- Story code snippets up to 8 lines; trail code snippets up to 14 lines
- With `--repo`, every snippet must match the file at `commit` (whitespace aside), and every `path:line` must exist

## Values
- **proof**: `reproduced-real` | `reproduced-isolated` | `read-in-code` | `inferred`
- **repro.proof**: the four above, plus `not-reproduced` | `skipped`
- **issueType**: `bug` | `test` | `feature` | `performance` | `docs` | `refactor` | `security`
- **trail role**: `start` | `path` | `problem` | `change`

Only use `reproduced-real` anywhere if `repro.proof` is `reproduced-real`.

## Top level

| Field | Required | Notes |
|---|---|---|
| `repo` | yes | `host/owner/repo`, e.g. `github.com/acme/app` or `gitlab.com/group/sub/project`. The host is used to build file links. |
| `issue` | yes | Issue id as a string. |
| `issueUrl` | | Link to the issue. |
| `commit` | recommended | Full sha the page was built against. Used for file links and line checks. |
| `fileUrlTemplate` | | For hosts not auto-detected: e.g. `https://git.example.org/x/y/blob/{commit}/{path}#L{start}-{end}` |
| `issueType` | recommended | See values. |
| `language` | | BCP-47 code of the page's language, e.g. `en`, `es`, `hi`. |
| `plainTitle` | yes | The issue in plain words. |
| `tldr` | yes | `{problem, cause, change}`, one short line each. `change` names the file(s). |
| `chapters` | recommended | Chapter names in order. The "why" chapter is named for the issue type (see SKILL.md). |
| `counts` | | `[{label, value, explain}]`. Every count needs `explain` so the numbers add up. |
| `notes` | | `["text"]` or `[{kind, text}]`. `kind` is `note` (default), `warn` (not verified) or `safety` (a rule that protects the contributor's machine or data, citing its file). Shown as plain sentences under the summary. |
| `aiPolicy` | when the repo has one | `{text, source}`: the project's rule on AI-assisted contributions, close to its own wording, and the file it came from. Shown above everything else on every tab. Leave it out if the repo sets no rule. |
| `glossary` | | `{term: "one-line definition"}`. Only project-specific terms a newcomer may not know. Terms are underlined wherever they appear. |

## story
```json
[{"chapter": "Problem", "steps": [{
  "title": "short heading",
  "simple": "...", "detailed": "...", "deep": "...",
  "proof": "read-in-code",
  "trail": "t2",
  "code": {"file": "path", "start": 120, "lines": "exact lines\njoined with \\n", "highlight": [123], "pointer": "note shown under the last highlighted line"},
  "links": [{"label": "...", "url": "..."}]
}]}]
```
At least `simple` or `detailed` per step. `highlight` lines must fall inside the snippet.

## trail (3 to 6 stops)
```json
[{"id": "t1", "plainLabel": "User runs the command", "label": "real symbol name",
  "file": "path", "start": 10, "end": 42, "role": "start",
  "whatItDoes": "one plain sentence",
  "code": {"start": 10, "lines": "...", "highlight": [12], "pointer": "..."}}]
```
`code` is required: the Code tab shows these exact lines (up to 14), with the key lines highlighted. With `--repo`, `render.py` fills `code.lines` from `file`, `start` and `end` when you leave it out, so you only need to add `highlight` and `pointer`. Otherwise copy the lines from the file at `commit`; for a stop with role `change` in a new file that doesn't exist yet, use the closest existing code it would sit next to.

## tests
`[{"file": "path", "names": ["test names"], "cases": 4, "casesExplain": "one per input file", "why": "one sentence"}]`. Give `cases` whenever the page shows counts.

## map
```json
{"note": "where this sits in the bigger system",
 "levels": [{"name": "repo", "items": [{"label": "src/", "onTrail": true}, {"label": "docs/", "onTrail": false}]},
            {"name": "src", "items": [...]},
            {"name": "file.ext", "items": [{"label": "fn()", "onTrail": true, "trail": "t3"}]}]}
```
Each level zooms into the on-trail item from the level above. Items with `trail` open that stop in the Code tab.

## flow
```json
{"actors": ["2 to 5 real component names"],
 "messages": [{"from": "A", "to": "B", "label": "short call", "say": "one plain sentence", "trail": "t1", "bad": false, "async": false}],
 "simplified": true,
 "omitted": ["steps left out of the diagram"],
 "variants": {"label": "what differs",
   "before": {"name": "old version", "overrides": {"2": {"label": "...", "say": "...", "bad": false}}},
   "after": {"name": "new version", "overrides": {}}}}
```
`from` and `to` must be actors (the same actor for both draws a self-call). The run stops on the first `bad` message. `simplified: true` requires `omitted`. Keep steps that cause the issue (waits, retries, races) in the diagram. Override keys are message indexes.

## mismatch
`{"label": "what is compared", "expected": "...", "actual": "...", "explain": "one line"}`, shown as a character-level diff.

## why
`{"proof": "...", "simple": "...", "detailed": "...", "deep": "..."}`

## history
`[{"when": "YYYY-MM-DD", "what": "...", "url": "..."}]`

## scope
`{"needsChange": ["precise areas"], "noChangeNeeded": ["precise areas, e.g. 'production library code (no change appears necessary)'"]}`

## changePlan
```json
{"where": "path:symbol",
 "keepChecking": ["what must still be guaranteed"],
 "allowToVary": ["what may legitimately change"],
 "firstTry": "one concrete first step, in words",
 "mustNotBreak": ["up to 3"],
 "testToExtend": "path, and what case to add",
 "commands": [{"label": "Run just these tests", "cmd": "...", "inferred": false}],
 "checklist": ["..."],
 "repoRules": ["contribution rules that matter for this change"],
 "prTips": ["..."]}
```
If `allowToVary` is set, `keepChecking` is required. Put AI-use rules in the top-level `aiPolicy`, not here.

## Proof records
- `repro`: `{"proof", "ran": "what was run", "notRun": "what was not run, and why", "command", "output"}`. `notRun` is required unless `proof` is `reproduced-real`.
- `proofLog`: `[{"claim", "proof", "evidence": "path:line or URL or short description"}]`. `path:line` evidence is checked with `--repo`.

## Extras
- `pastFixes`: `[{"title", "url", "why"}]`
- `questions`: `["worded so they can be posted on the issue"]`
- `ignore`: `[{"file", "why"}]`
