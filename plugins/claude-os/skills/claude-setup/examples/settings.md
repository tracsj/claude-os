# settings.json Example

## Content + web project

```json
{
  "permissions": {
    "allow": [
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(git branch:*)",
      "Bash(git checkout:*)",
      "Bash(git merge:*)",
      "Bash(git push:*)",
      "Bash(git fetch:*)",
      "Bash(git pull:*)",
      "Bash(git stash:*)",
      "Bash(npm run:*)"
    ],
    "deny": [
      "Bash(git push --force:*)",
      "Bash(git reset:*)",
      "Bash(git clean:*)",
      "Bash(git restore:*)",
      "Bash(rm -rf:*)",
      "Read(.env)",
      "Read(.env.*)",
      "Read(**/secrets/*)",
      "Read(**/*credential*)",
      "Read(**/*.pem)",
      "Read(**/*.key)"
    ]
  }
}
```

## Why not `Bash(git:*)`?

That broad pattern auto-allows destructive operations: `git reset --hard`,
`git clean -fd`, `git checkout -- .`, `git restore .`. The granular allow
list above covers all everyday git work while keeping those in the
"needs approval" bucket. The deny list adds a second layer for the
highest-risk commands (`reset`, `clean`, `push --force`).

## Why not `Bash(git -C /path:*)`?

This looks appealing — path-scoped, convenient for multi-repo sessions. But
`-C` just changes directory; the `*` still covers every subcommand. `git -C /path reset --hard` auto-approves without a prompt. Per-subcommand rules are the correct solution here too.

## Why not `Bash(git -C * status*)` — the per-subcommand version?

Adding the subcommand back does not fix it, because the wildcard now sits where `git`'s own options go and spans everything up to `status`. Several `git -c` config keys execute commands: `core.pager` on `log`/`diff`/`show`, `core.sshCommand` on `fetch`, `core.fsmonitor` on index refresh, which reaches `status`, `add` and `commit`. So `git -C . -c core.pager='curl … | sh' log` matches the rule and runs unprompted.

Narrowing the path does not help either — `Bash(git -C /Users/me/* status*)` still lets the options ride inside the wildcard. **A wildcard is only safe AFTER the subcommand.** For multi-repo work, write one exact-path rule per repo per subcommand (`Bash(git -C /Users/me/.claude status*)`), or accept a prompt for the repos you rarely touch. Deny rules are the exception: an over-matching deny is safe, so leave those wildcarded.

## Why not `Bash(node:*)`?

`node:*` is unrestricted code execution — a script run this way can read
`.env.local` (or anything else) and exfiltrate it, bypassing every `Read` deny
rule above. It is on the blocklist in `CONVENTIONS.md` §7 for exactly
this reason. For a JS/TS project, `Bash(npm run:*)` already covers running the
project's own scripts; if a session needs a new capability, add the narrowest
rule that grants it (a specific `npm run <script>`, a scoped CLI), never the
open `node:*` wildcard. The same applies to `python3:*`, `uv run:*`,
`npx:*`, `bunx:*`, and unscoped `curl:*`.
