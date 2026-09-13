# NOTES.md

## CLAUDE.md choices

I kept `CLAUDE.md` to four short sections: a one-line description, commands, conventions, and architecture.

What's in it:
- **Commands** — `npm run dev`, `npm test` (plus how to run a single test), `npm run lint`, since these are run every session and the single-test flag isn't obvious from `package.json` alone.
- **Conventions** — the route-per-resource pattern, routing all data access through `db/store.js`, JSON error shape, and the `require.main === module` guard in `server.js`. These are real rules that would otherwise require reading multiple files to infer, and following them keeps new code consistent with the existing routes.
- **Architecture** — a few lines on how `server.js`, `routes/`, `db/store.js`, and `tests/` fit together.

What I left out:
- The full course task list and setup steps from `README.md` — that's onboarding for a human, not something Claude needs on every task.
- Details of `.env.example` and the ESLint config — Claude can read those files directly when relevant; restating them would just be duplication that goes stale.
- Anything about the CI workflow — it's a one-off detail visible in `.github/workflows/ci.yml`, not a standing rule.
- No secrets or long pasted documents — there weren't any to begin with, but I kept that constraint in mind while writing.

The goal was that every line earns its place: things that are true today, not obvious from a quick read of the code, and reusable across tasks.

## Permission rules

In `.claude/settings.json`:
- **Allow**: `Bash(npm test:*)` and `Bash(npm run lint:*)` — safe, frequently-run, side-effect-free commands. Auto-allowing them avoids repeated prompts for routine checks.
- **Ask**: `Bash(git push:*)` — pushing affects the shared remote, so I want a chance to confirm before it happens, even though it's routine.
- **Deny**: `Read(./.env)` and `Bash(git push --force:*)`.

Without the deny rules:
- Claude could read `.env` and end up quoting real secrets (database URLs, API keys) back in chat output, logs, or a commit — even though `.env` is git-ignored, the contents would still leak once read into a conversation.
- Claude could force-push over a branch, silently discarding commits (mine or a collaborator's) with no easy way to recover them.

`.claude/settings.json` is committed so these rules apply for anyone working in the repo; `.claude/settings.local.json` (already git-ignored) is where personal overrides would go instead.

## Verification

- `/memory` shows `CLAUDE.md` loaded from the project root.
- `/permissions` shows the allow/ask/deny rules above.
- Asking "How do I run the tests here?" gets answered from `CLAUDE.md` (`npm test`) without further explanation needed.
