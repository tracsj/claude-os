# claude-os

Six skills for building and maintaining a Claude Code setup, packaged as a plugin.

```bash
claude plugin marketplace add tracsj/claude-os
claude plugin install claude-os@tracsj
```

---

## How this came about

I started working with Claude Code and kept noticing the same thing. There were genuinely useful capabilities arriving — skills, hooks, memory, subagents — and nothing was enforcing any of them. Whether a session did good work depended on whether I happened to remember the right practice that day.

Some of the practice here started with Boris Cherny, who created Claude Code, and a line of his that I keep coming back to: *"Every single time Claude makes a mistake, I don't tell it to do it differently. I tell it to write it to the CLAUDE.md, or make a skill, or something."* Correcting a model in chat fixes one run. Writing the correction down fixes every run after it.

What I wanted was the next step from that. Not only a place to write things down, but a layer that improves as I use it — so that when I learn to do something well in one repository, that practice travels to the next one without my having to remember it, re-derive it, or point a new project at an old one and ask it to work out what was good in there.

That is what this is. A living layer of functionality, sitting above eleven projects, that gets better because I keep working in it.

## What has held up

I use this system extensively whenever I create a new repository, which is the honest test of whether a convention is real or merely written down. A practice that only survives while you are thinking about it is not a practice.

The part I did not expect to matter as much as it does is the learning loop. Every skill opens by reading its own `learnings.md` and closes by asking whether anything belongs there, and entries are promoted into the skill itself and then deleted rather than left to pile up. Two skills sit on top of that loop and are the reason it does not silt up: one that cleans house internally — consolidating learnings across skills, pruning stale entries, checking the memory layer is still coherent — and one that looks outward at what has changed in the ecosystem and asks what is worth adopting. Cleaning and upgrading turn out to be different jobs, and a system that only does the first slowly falls behind while a system that only does the second accumulates everything it ever tried.

Neither of those two ships here, because both are built around my own projects and would be inert for anyone else. What ships is the layer underneath them, which is the part that transfers.

## What rotted, and what fixed it

The thing that took longest to accept is that writing it down is not always enough.

There is a rule in one of my repositories saying that file edits go through the editing tool rather than through a shell script, because a bad regex fails silently where the editing tool fails loudly. I wrote it, then wrote it more emphatically, and it kept getting broken — not occasionally, but in about a quarter of all file changes in that project. The instruction had been loaded into context on every single one of those occasions. More emphasis was never going to fix it.

What fixed it was a hook — fifty lines of code that inspect the command before it runs and refuse the ones that write to a source file. It has not been broken since, because it cannot be. The file is twice that length, because the explanation of why it exists is longer than the code that enforces it.

That is the distinction the rest of this README is organized around, and it is the one I would want a team to understand before anything else. Instructions in `CLAUDE.md` are context — delivered to the model, shaping what it does, followed most of the time. Hooks and permission rules are enforcement, and they hold regardless of what the model decides. Most of the frustration people have with agent setups comes from having put something in the first category that belonged in the second. The tell is that you find yourself rewriting a rule in stronger and stronger language.

---

## If you were doing this for a team

Everything above is one person's configuration. The question that follows is how any of it survives contact with other people — how a company gets a single set of conventions that actually reaches every session, rather than a wiki page nobody opens.

Claude Code has four layers for this, and they do genuinely different jobs.

**Managed-policy `CLAUDE.md`.** A file deployed by MDM or configuration management to a fixed path on every machine — `/Library/Application Support/ClaudeCode/CLAUDE.md` on macOS, `/etc/claude-code/CLAUDE.md` on Linux. It loads into every session in every repository on that machine, and individual settings cannot exclude it. This is where organization-wide non-negotiables live: security posture, compliance reminders, data-handling rules. There is also a `claudeMd` key inside `managed-settings.json` if you would rather ship the content inline than distribute a separate file.

**Project `CLAUDE.md`, at the root and per subdirectory.** Committed to the repo, so it travels with the code and gets reviewed like the code. Files are discovered from the filesystem root down to the working directory and concatenated, so the most specific instructions are read last. Architecture rules and security boundaries go at the root; conventions specific to one area go in that directory.

**`.claude/rules/`.** Topic files that can be scoped to file patterns with `paths:` frontmatter, so a rule only enters the context window when Claude touches a matching file. They also support symlinks, which is the cheap way to share one canonical rule across many repositories:

```bash
ln -s ~/company-standards/security.md .claude/rules/security.md
```

**A plugin in a private marketplace.** The versioned, installable layer — and the only one that carries *behavior* rather than instructions. A marketplace is a JSON file listing plugins and where to fetch them; host it in a private repository and it stays internal to your organization. Managed settings can force-enable a plugin, so a design system or a review process can ship as something nobody has to remember to turn on.

### The distinction that matters most

`CLAUDE.md`, rules and skills are **context**. They are delivered to the model and they shape what it does. They are followed most of the time and not all of the time.

Hooks and permission rules are **enforcement**. They run at fixed lifecycle events or block a tool call outright, regardless of what the model decided.

So for any rule, the question is which layer it belongs to — and the tell that you have chosen wrong is that you find yourself rewriting the rule in progressively stronger language. Emphasis is not a mechanism.

## The learnings loop

Every skill here opens by reading its own `learnings.md` and closes by asking whether anything is worth recording there. Entries go under `## Staging` with a date, and they do not accumulate: anything likely to recur gets promoted into the skill itself and **the staging entry is deleted**. Anything that has sat unpromoted for a month gets deleted too, on the grounds that it evidently was not worth keeping.

The skills in this plugin ship with those files **empty**. The mechanism is the transferable part; the incidents that filled mine are specific to my projects and would only be noise in yours.

The full set of conventions the skills assume — the folder layout, the promotion rules, the permission blocklist and the reasoning behind it, and how to decide where a rule lives — is in [`plugins/claude-os/CONVENTIONS.md`](plugins/claude-os/CONVENTIONS.md).

## The skills

| Skill | Use it when |
|---|---|
| `claude-setup` | Standing up a `.claude/` folder for a new project — context files, folder structure, permissions, first skills |
| `claude-setup-subfolder` | Adding a work area inside a project that already has a `.claude/` folder |
| `build-skill` | Authoring a new skill, with the structure and the checks that are easy to miss by copying a good example |
| `build-agent` | Defining a subagent, and deciding whether an agent is even the right shape |
| `shape-app` | Front-loading the hard-to-reverse decisions — schema, entity relationships, source of truth, domain vocabulary — before building features |
| `app-ux-audit` | Auditing a running web app against the details that separate software that works from software that feels finished |

Six skills cost roughly 830 tokens of always-on context; the body of each loads only when it fires.

## Installing

The two commands are at the top of this file. A few things worth knowing before you run them.

Skills are namespaced, so they arrive as `/claude-os:build-skill` and will not collide with anything you already have. To try it without installing anything:

```bash
claude --plugin-dir ./plugins/claude-os
```

MIT licensed. If you fork it and change the conventions, change `CONVENTIONS.md` too — the skills reference it, and a plugin whose skills cite rules it no longer follows is worse than one with no rules at all.
