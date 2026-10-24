# spl-asdlc-livela-sandbox

The sandbox where the [SPL-ASDLC](https://github.com/Martin-Rancourt/spl-asdlc) flow is exercised end to end **with Livela as the tracker**: the project is managed in Livela, SPL-ASDLC is turned on from Livela's guided setup, and the runner on the box is the only bridge. It follows the `spl-asdlc` version Livela embeds (the workflow callers on `@vX.Y`), not a staging channel: it tests what Livela ships. The end-to-end scenario, step by step: [`SCENARIO.md`](SCENARIO.md).

What it holds at the start, and nothing else:

- A tiny Rust crate (`src/lib.rs`: `greet` and its unit tests), so that a ticket has something real to change and CI something real to run.
- Its CI (`.github/workflows/ci.yml`): rustfmt, clippy and the tests on every push to `main`.

What the method adds comes from Livela's guided setup, in one PR the owner opens from Livela: `.spl-asdlc/config.toml` (the light configuration: no PR review, the release cycle with no QA angle), the workflow callers, `.spl-asdlc/process.md` and `AGENTS.md`. The identities of the installation (repository, owner, tracker project) stay in Livela, never in this repository.

Nothing is published from here: a release only tags and publishes its notes.

## Develop

The three checks CI runs, in the same order:

```sh
cargo fmt --all --check
cargo clippy --all-targets --locked -- -D warnings
cargo test --locked
```
