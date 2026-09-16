---
name: rust-systems-engineering
description: Rust systems engineering reference. Use when working in Rust, Cargo workspaces, unsafe code, FFI, concurrency, async runtimes, OS interfaces, embedded or no_std targets, or performance-sensitive systems code.
---

# Rust Systems Engineering

Use this reference alongside the workflow skill already driving the task. It sharpens implementation, testing, debugging, design, and review for Rust systems code without replacing the repository's own standards.

## Read the build surface first

Treat the repository as the source of truth. Inspect `Cargo.toml`, workspace manifests, `rust-toolchain*`, `.cargo/config*`, `build.rs`, CI, `AGENTS.md` or `CLAUDE.md`, and existing scripts before choosing commands.

- Preserve the declared MSRV, targets, feature policy, and toolchain.
- Use existing checks and tools. Do not install a Cargo subcommand or add a dependency unless the user approved that change.
- Distinguish workspace-wide requirements from one crate's requirements.
- Record which target, feature set, runtime, and operating system a result applies to.

## Contracts

For every changed interface, account for the contract a caller must uphold and can rely on:

- valid and invalid inputs
- ownership, borrowing, and lifetime relationships
- panic and error behavior
- blocking, cancellation, and shutdown behavior
- thread-safety and reentrancy
- resource acquisition and release
- performance characteristics that callers depend on
- platform, target, feature, and MSRV limits

Use types to make invalid states unrepresentable where that reduces caller knowledge. Newtypes, enums, typestates, and capability-bearing handles are useful when they remove a runtime invariant from every caller. Avoid type machinery that only moves complexity into bounds, conversions, or compiler errors.

## Validation ladder

Run the smallest check that can disprove the current change, then widen only after it passes:

1. The narrowest relevant test, example, harness, or compile-fail case.
2. The changed crate's normal check and test commands.
3. Relevant feature combinations and target-specific checks already required by CI.
4. Workspace checks, tests, and documentation tests.
5. Existing specialist checks such as Miri, fuzzing, sanitizers, Loom, benchmarks, or cross compilation.

Do not claim coverage for a feature combination, target, sanitizer, or specialist tool that was not run.

## Unsafe and FFI

Treat each `unsafe` block, `unsafe fn`, unsafe trait implementation, and FFI boundary as a proof obligation.

- State the safety invariant close to the unsafe operation.
- Verify pointer validity, alignment, initialization, provenance, aliasing, and lifetime.
- Check integer conversions used for lengths, offsets, discriminants, and allocation sizes.
- Keep unsafe regions small, then expose a safe interface that enforces their preconditions.
- For `Send` and `Sync`, reason about every reachable field and external resource, not only Rust types.
- For FFI, verify ABI, layout, ownership transfer, allocation and deallocation pairing, string encoding, callback lifetime, and nullability.
- Prevent unwinding across an FFI boundary unless the ABI and both sides explicitly support it.
- Treat generated bindings and external C or C++ headers as part of the contract.

## Concurrency and async

Trace ownership and progress across threads and tasks.

- Name shared mutable state and the synchronization protecting it.
- State lock ordering when more than one lock can be held.
- Do not hold a synchronous lock across `.await`.
- Separate blocking work from async executor threads.
- Check cancellation at every await point that can leave state or resources half-updated.
- Make shutdown behavior explicit: who signals, who drains, who joins, and what happens on timeout.
- For atomics, justify the chosen ordering through a happens-before argument. "It seems sufficient" is not a proof.
- Prefer deterministic concurrency tests or existing tools such as Loom over sleep-based timing tests.

## Operating-system and resource boundaries

Model resources as owned values with one clear cleanup path.

- Handle partial reads and writes, interruption, short buffers, EOF, and retry semantics.
- Check descriptor, handle, socket, process, mapping, and temporary-file lifetimes.
- Preserve the original error context when translating OS or library errors.
- Account for signals, process exit, child reaping, and inherited handles where relevant.
- Treat endianness, alignment, path encoding, filesystem semantics, and `cfg` differences as behavior, not build trivia.
- Keep platform-specific code behind a narrow interface, but do not add a trait only to imitate dynamic dependency injection.

## Performance

Measure before changing performance-sensitive code.

- Establish a repeatable baseline with existing benchmarks, profilers, traces, or a minimal timing harness.
- Separate throughput, latency, tail latency, memory, allocations, syscalls, cache behavior, and contention.
- Look for accidental cloning, allocation, boxing, dynamic dispatch, buffering, and lock contention.
- Keep correctness and benchmark configurations equivalent.
- Report noise, sample size, environment, target, feature set, and build profile with the result.

## Rust review baseline

In addition to repository standards, inspect changed code for:

- unsafe code whose invariant is missing or not enforced by its safe wrapper
- panics, `unwrap`, or `expect` on library, service, daemon, callback, or input-driven paths
- accidental panic or unwind across thread, task, plugin, or FFI boundaries
- unchecked arithmetic, narrowing conversions, indexing, allocation sizes, and offset calculations
- locks held across `.await`, blocking work on async executors, deadlock risk, or unjustified atomic ordering
- cancellation or shutdown paths that leak resources or leave durable state half-updated
- panic in `Drop`, destructor ordering, or `ManuallyDrop` use that can violate cleanup or FFI ownership contracts
- deliberate or accidental leaks through `mem::forget`, `Box::leak`, reference cycles, or detached tasks
- unnecessary `clone`, allocation, boxing, `dyn`, interior mutability, or generic complexity
- public API, feature, target, MSRV, serialization, or ABI changes not named in the spec
- `cfg` branches and feature combinations that compile separately but violate one shared contract
- errors that lose source context or collapse actionable cases into strings

Treat these as evidence-driven review questions, not automatic findings. Repository conventions and measured constraints win.
