# Threat model — forgekit

## What this project does and where untrusted input enters
forgekit is a Node.js CLI that AI coding agents run locally. It reads the agent's
working repo (source files, git state, config files like `brand.json`) and emits
native config for editors/agents. Untrusted input enters via:
- repo files it scans (filenames, file contents — including adversarial repos)
- `forge verify` claim inputs (agent-generated claims about code)
- guard hook scripts under `global/guards/` (executed by the agent harness)

## Components that matter most / least
- **Most:** `src/verify.js` (claim verification logic), `global/guards/run.mjs`
  (hook execution), `src/commit_gate.js` (pre-commit checks), anything that
  executes shell commands or writes files.
- **Less:** docs rendering, changelog generation, demo scripts.
- `node_modules/` is third-party and out of scope.

## How to exercise it
- `node src/cli.js --help` lists commands.
- `npm test` runs the full suite (node --test).
- Key binaries: `forge verify`, `forge precommit`, `forge impact`.

## How you rate severity
- Command injection / arbitrary file write via crafted repo content: **critical**.
- Path traversal in file operations: **high**.
- DoS via crafted input (e.g. regex blowup on large files): **medium**.
- Anything requiring the attacker to already control the agent's machine: **low**
  (the tool is designed to run with the user's own privileges).

## Anything to leave alone
- Do not report missing signature verification on npm installs (out of scope;
  npm handles that). Do not report that guards *can* be bypassed by editing
  local files (the threat model assumes the local user is trusted; guards
  protect against agent mistakes, not a malicious operator).
