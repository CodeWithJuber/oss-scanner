# Threat model — hikmah-stack

## What this project does and where untrusted input enters
hikmah-stack is a deterministic Rust memory/reliability kernel for AI agents.
The `hikmah` binary stores and recalls agent memory records (JSON) with an
append-only audit ledger. Untrusted input enters via:
- `hikmah remember` record payloads (arbitrary JSON from the agent)
- `python/hikmah/` bridge inputs (subprocess calls into the binary)
- ledger files read back by `hikmah recall` / `hikmah why`

## Components that matter most / least
- **Most:** `runtime/hikmah-kernel/src/` — admission logic, ledger append,
  `principal.rs` (identity), anything parsing JSON or touching the filesystem.
- **Less:** bench/, examples/, docs/.
- `target/` build artifacts are out of scope.

## How to exercise it
- `cargo run -p hikmah-kernel -- --help`
- `cargo test --workspace` (171 tests).
- `hikmah remember --help`, `hikmah recall --help`.

## How you rate severity
- Memory-safety issues (the crate is pure safe Rust; any `unsafe` finding is
  **high** minimum): **critical** if exploitable.
- Ledger tampering / forgery (breaks the audit guarantee): **high**.
- DoS via crafted record (e.g. huge JSON): **medium**.
- The kernel runs with the invoking user's privileges; privilege escalation
  beyond the user is out of scope.

## Anything to leave alone
- Do not report that the ledger is not encrypted at rest (documented design;
  encryption is the operator's job). Do not report `#[ignore]` tests as
  findings.
