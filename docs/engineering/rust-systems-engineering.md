## What it does

`rust-systems-engineering` adds the engineering checks that general application workflows usually miss in Rust systems code: ownership contracts, unsafe proof obligations, FFI, concurrency, resource lifetime, target compatibility, and measured performance.

It is a reference, not a process. It does not replace [tdd](https://aihero.dev/skills-tdd), [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs), [codebase-design](https://aihero.dev/skills-codebase-design), or [code-review](https://aihero.dev/skills-code-review). Those skills pull it in when a Rust or systems boundary makes its questions relevant.

## When to reach for it

Type `/rust-systems-engineering`, or the [agent](https://www.aihero.dev/ai-coding-dictionary/agent) reaches for it automatically when work involves Rust, Cargo, unsafe code, FFI, concurrency, async runtimes, operating-system interfaces, embedded or `no_std` targets, or performance-sensitive systems code.

| Situation | What this reference adds |
| --- | --- |
| Designing a Rust interface | Ownership, lifetime, panic, cancellation, cleanup, feature, target, and performance contracts |
| Reviewing unsafe or FFI code | A proof-obligation checklist for validity, layout, ABI, ownership transfer, unwinding, and `Send` or `Sync` |
| Debugging a race or systems failure | Deterministic schedules, traces, core dumps, sanitizers, system calls, and resource evidence |
| Testing a crate or workspace | A validation ladder from one narrow test through workspace, feature, target, and existing specialist checks |
| Optimizing a hot path | A measured baseline separated into latency, throughput, allocation, syscalls, cache behavior, and contention |

## The contract is larger than the type

Rust catches a large class of mistakes, but a compiling signature does not state the whole interface. Callers may still need to know whether an operation blocks, can panic, survives cancellation, owns a descriptor, requires one feature, works on one target, or preserves an ABI.

This skill makes those facts part of the **contract**. The design is sharper when types remove caller obligations, but type machinery is not free. A newtype, enum, typestate, generic, or trait earns its place only when it reduces knowledge or runtime risk for callers.

## Validation follows the risk

The skill widens validation one rung at a time:

1. Run the narrowest example, test, compile-fail case, harness, or benchmark that can disprove the change.
2. Run the changed crate's normal checks and tests.
3. Cover relevant feature combinations and target checks already required by CI.
4. Run workspace checks, tests, and documentation tests.
5. Run existing specialist tools such as Miri, fuzzing, sanitizers, Loom, benchmarks, or cross compilation when the risk calls for them.

It never claims coverage for a target, feature combination, or specialist tool that was not run, and it never installs a missing tool or dependency without approval.

## Common questions

**Does this force every Rust project to run Miri, fuzzing, sanitizers, and every feature combination?**

No. The repository defines its supported surface. The skill uses specialist checks that already exist and match the changed risk. A safe parser change may need a property test; an FFI ownership change may need Miri or a sanitizer; a documentation edit needs neither.

**Does every external dependency need a trait wrapper?**

No. A trait is not automatically a seam, and one implementation is not automatically a design failure. Use a concrete type when nothing needs to vary. A single-adapter seam can still be real when it isolates an external contract, platform code, unsafe code, ownership, or a required compile-time substitution.

**Will it add crates or Cargo subcommands to get better coverage?**

Not by default. It reads manifests, CI, repository instructions, and existing scripts first. Adding a dependency or installing a tool changes the project or environment and requires explicit approval.

**Is this only for unsafe Rust?**

No. Safe Rust still has cancellation bugs, deadlocks, resource leaks, partial I/O, platform differences, accidental panics, compatibility breaks, and performance regressions. Unsafe and FFI add proof obligations, but they are only two branches of the skill.

## It's working if

- A changed interface names ownership, failure, panic, cancellation, cleanup, compatibility, and performance constraints that callers depend on.
- Unsafe code has a local invariant and a safe wrapper that enforces its preconditions.
- Concurrency reviews name shared state, synchronization, lock ordering, cancellation, and shutdown rather than saying only "thread-safe".
- Validation reports exactly which workspace members, features, targets, profiles, and specialist checks ran.
- Performance claims include a baseline and preserve the same environment, input, target, features, and build profile.

## Where it fits

`rust-systems-engineering` is a model-invoked reference underneath the engineering flows. [implement](https://aihero.dev/skills-implement), [tdd](https://aihero.dev/skills-tdd), [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs), [prototype](https://aihero.dev/skills-prototype), [codebase-design](https://aihero.dev/skills-codebase-design), [code-review](https://aihero.dev/skills-code-review), [to-spec](https://aihero.dev/skills-to-spec), and [to-tickets](https://aihero.dev/skills-to-tickets) pull it in when the repository or boundary is Rust systems work. [ask-matt](https://aihero.dev/skills-ask-matt) maps the whole set.
