# Conventions

The six skills in this plugin assume a handful of shared conventions. They developed over about a year of running one Claude Code configuration across eleven projects, and each of them exists because something went wrong first.

If you install the plugin and want the skills to behave as designed, these are the assumptions they make. If you'd rather run your own conventions, the skills still work — you'll want to adjust the closing section of each `SKILL.md`, which is where most of this is referenced.

---

## 1. A skill is a folder, not a file

```
.claude/skills/<name>/
  SKILL.md          ← the hub; targets under 200 lines
  learnings.md      ← the log; starts empty
  [examples/]       ← worked examples, if any exist
  [templates/]      ← things the skill produces
  [references/]     ← checklists and deep reference
```

`SKILL.md` is a router. It carries the process and the decisions, and defers detail to spoke files that load only when the skill actually needs them. A 600-line `SKILL.md` costs context on every invocation whether or not the deep material is relevant.

The practical test: if a section is only read during one step, it belongs in a spoke.

**One thing to know about spoke files in an installed plugin.** The skill body is delivered to the model automatically, but a spoke file has to be *read*, and an installed plugin lives outside your project — under `~/.claude/plugins/cache/…`. In an interactive session that produces a permission prompt the first time, which you approve and move on. In a headless run (`claude -p`) it fails silently unless you pass `--add-dir` pointing at the plugin, because file access is scoped to the working directory and a denied read looks exactly like a missing file.

This applies to a skill reading its own spokes, not only to the cross-skill references in `build-skill` and `build-agent`. Worth knowing before you conclude a plugin is broken: measured here, the paths resolved correctly every time and only the directory scope was in the way.

## 2. Every skill opens by reading its learnings and closes by writing them

The first line of every `SKILL.md`:

> Before starting, read learnings.md and apply any guidance from the Staging section.

The last section of every `SKILL.md` asks whether anything is worth recording, and appends a dated entry under `## Staging` in `learnings.md`.

This is the loop that makes a skill improve rather than ossify. It is also the part most often dropped when someone writes a skill by hand from a good example — the structure is easy to copy by eye and the checks are not.

## 3. Staging is a holding pen, not an archive

`learnings.md` has exactly one active section, `## Staging`. Entries do not accumulate there. Each one is resolved:

| Situation | What happens |
|---|---|
| Likely to recur | Promote into `SKILL.md` (or a spoke) now, and **delete the Staging entry** |
| Seen in three or more sessions | Promote if it hasn't been already, and delete the entry |
| One-off | Leave it in Staging |
| Older than ~30 days, still unpromoted | Delete it. If it hasn't recurred, it wasn't worth keeping |

**Promotion is not finished until the source entry is deleted.** Leaving both copies is how a skill file doubles in size while saying nothing new. The rule goes in the skill; the incident that produced it does not travel with it.

Keep Staging to a handful of live observations.

## 4. Every skill needs a verification step

If a skill produces an output, it must be able to check that output before presenting it. Without that, the person you built the skill for becomes the feedback loop, and they will find the same class of error every time.

A verification step is stronger the more mechanical it is. Prefer a script that exits non-zero to a checklist a model reads, and prefer a checklist to nothing. Where the thing being checked is a judgement rather than a fact, print the evidence and rank nothing — a thresholded judgement fires on good work, which trains the reader to skim the whole finding class.

## 5. The description is the trigger, and the negative trigger does more work

Skill descriptions are written for the model, not for a human browsing a list. Include the phrases that should invoke the skill.

Then include what should *not* invoke it. Negative triggers (`Do NOT use for…`) prevent unwanted activation more reliably than positive triggers cause wanted activation, because the failure they prevent is silent: a skill firing on an adjacent task produces confident, well-structured, wrong work.

## 6. Guidance and enforcement are different layers

This is the distinction that took longest to learn and matters most at team scale.

`CLAUDE.md` and skills are **context**. They are delivered to the model and shape what it does. They are followed most of the time, not all of the time, and no amount of emphasis changes that — a rule that has been loaded on every one of the occasions it was broken is not going to start working because it gets bolded.

Hooks and `settings.json` permission rules are **enforcement**. They run as shell commands at fixed lifecycle events, or block a tool call outright, regardless of what the model decided.

So the question for any rule is which layer it belongs to. "Prefer named exports" is guidance. "Never edit `.env`" is enforcement. If you find yourself writing a rule in progressively stronger language, that is the signal it was the wrong layer to begin with.

## 7. Permission rules: the narrowest thing that works

The skills in this plugin will not propose a broad permission rule, and `claude-setup` explicitly refuses several. The patterns worth banning outright:

- **`Bash(node:*)`, `Bash(python3:*)`, `Bash(curl:*)`** and the package-manager runners (`npx`, `bunx`, `uv run`, `poetry run`, `pipenv run`). Each is unrestricted code execution. A script run this way reads any file it likes, which bypasses every `Read` deny rule you wrote.
- **`Bash(git:*)`** — auto-approves `reset --hard`, `clean -fd`, `checkout -- .`. Use per-subcommand allows.
- **Any wildcard *before* the subcommand.** `Bash(git -C * status*)` looks path-scoped and is not: the wildcard sits where git's own options go, and several `-c` config keys execute commands (`core.pager` on `log`, `core.sshCommand` on `fetch`). So `git -C . -c core.pager='curl … | sh' log` matches that rule and runs without a prompt. **A wildcard is only safe after the subcommand.** Deny rules are the exception — an over-matching deny is safe, so leave those wildcarded.
- **`Write(path)` rules for files.** They are silently unenforced. Use `Edit(path)`, which already covers every file-editing tool.

One more, easy to miss: **a permission change is not in force in the session that writes it.** Rules load at session start. If you want to verify a new deny rule, verify it against a synthetic fixture in a fresh session, never against a real credential file — because if the rule isn't active yet, the probe is the exposure.

## 8. Deciding where a rule lives

When something is worth writing down, it goes in exactly one of four places. A rule earns a spot in the **user-scope `~/.claude/CLAUDE.md`** — loaded into every session in every project — only if all three hold:

1. It is a behavioral guardrail that prevents a recurring mistake, rather than reference knowledge for a specific scenario.
2. It would cause a mistake in *typical* sessions, not only under rare conditions.
3. It plausibly applies at working frequency in two or more active projects.

If not all three, route it instead to the skill's own gotchas (scenario-specific), the project's `CLAUDE.md` (single-project concern), or a reference file (repair procedures).

That filter governs *whether* a rule is admitted, and says nothing about how many words it brings with it. Both matter. A hub file that admits only correct rules can still become unusable if each one arrives with its full incident narrative attached — keep the action in the hub and move the justification, the mechanism and the tell to a spoke file.

---

## What this plugin does not ship

Two skills referenced in passing by the templates here are not included, because their working versions are specific to one person's projects:

- **`housekeeping`** — a periodic audit that consolidates cross-skill learnings, prunes stale Staging entries, and checks memory integrity. The generated `wrap-up` template mentions it as an optional step; treat that as a placeholder for whatever periodic audit you build.
- **`systems-upgrade`** — a recurring scan of the Claude Code ecosystem, cross-referenced against a profile of your own stack. The profile is the whole skill, and yours would share nothing with mine.

Both are described here so the references in the templates make sense rather than looking like broken pointers.
