# Threat model — wisdomlens

## What this project does and where untrusted input enters
wisdomlens is a set of agent skills (markdown + small node scripts) for
Claude Code / Codex / Cursor. The skills are prompt text the agent reads;
the scripts are validators and hooks (`scripts/`, `hooks/`). Untrusted input
enters via:
- hook scripts that receive tool-call JSON from the agent harness
  (`scripts/tripwire-check.mjs --hook`)
- skill content is static markdown (no runtime parsing of untrusted data)

## Components that matter most / least
- **Most:** `scripts/tripwire-check.mjs` (parses hook JSON from stdin),
  `hooks/hooks.json` (wires into the agent), `scripts/validate.mjs`.
- **Less:** skill markdown prose, reference docs.
- This repo has no network code, no crypto, no auth.

## How to exercise it
- `node scripts/validate.mjs`, `node scripts/trigger-test.mjs`,
  `node scripts/skill-eval.mjs`.

## How you rate severity
- Command injection via crafted hook JSON: **high**.
- Anything in the markdown prose (prompt-injection *advice*) is documentation,
  not a vulnerability — do not report it.
- DoS via large hook payloads: **low**.

## Anything to leave alone
- Do not report that skills are "prompt injection surface" — that is their
  documented purpose (they are instructions the user installs deliberately).
  The install-trust checklist in SECURITY.md covers this.
