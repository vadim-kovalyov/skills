---
name: tdd
description: Test-driven development. Use when the user wants to build features or fix bugs test-first, mentions "red-green-refactor", or wants integration tests.
---

# Test-Driven Development

TDD is the red → green loop. This skill is the reference that makes that loop produce tests worth keeping: what a good test is, where tests go, the anti-patterns, and the rules of the loop. Every section applies on every cycle: consult them before and during the loop, not after.

When exploring the codebase, read `GLOSSARY.md` (if it exists) so test names and interface vocabulary match the project's domain language, and respect ADRs in the area you're touching.

When the work changes Rust or Cargo code, or the behavior involves unsafe code, FFI, concurrency, async runtimes, operating-system boundaries, embedded targets, or performance constraints, call the Skill tool with "rust-systems-engineering" and apply its validation ladder.

## What a good test is

Tests verify behavior and invariants through the highest stable interface that can expose them. Code can change entirely; tests shouldn't. A good test reads like a specification: "decoder rejects a truncated frame without consuming the next frame" tells you exactly what capability exists, and it survives refactors because it doesn't care about internal structure.

See [tests.md](tests.md) for examples and [mocking.md](mocking.md) for mocking guidelines.

## Seams: where tests go

A **seam** is the stable boundary you test at: the interface where you observe behavior without coupling the test to incidental structure. Prefer public interfaces. In Rust systems work, a module-local test may reach a private item when that is the narrowest correct seam for an unsafe invariant, algorithm, parser state, or failure that cannot be observed faithfully above it. Treat it like any other seam: name the invariant and confirm the lower seam with the user before writing the test.

**Test only at pre-agreed seams.** Before writing any test, write down the seams under test and confirm them with the user. No test is written at an unconfirmed seam. You can't test everything, so agreeing the seams up front is how testing effort lands on the critical paths and complex logic instead of every edge case.

Ask: "What's the public interface, and which seams should we test?" Give each proposed seam a one-line note on what it catches and what it misses.

When the shape of that interface is itself in question (how deep the module is, where the seam belongs, what the interface should expose), call the Skill tool with "codebase-design" for the vocabulary. It is the shared source of the module, interface, depth, seam, adapter, leverage and locality terms, and it is a reference to consult, not a session to run.

## Anti-patterns

- **Implementation-coupled**: mocks internal collaborators, tests incidental private structure, or verifies through a side channel. The tell: the test breaks when you refactor but behavior or the named invariant hasn't changed.
- **Tautological**: the assertion recomputes the expected value the way the code does (`expect(add(a, b)).toBe(a + b)`, a snapshot derived by hand the same way, a constant asserted equal to itself), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth: a known-good literal, a worked example, the spec.
- **Horizontal slicing**: writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior: you test the _shape_ of things rather than user-facing behavior, the tests go insensitive to real changes, and you commit to test structure before understanding the implementation. Work in **vertical slices** instead: one test → one implementation → repeat, each test a **tracer bullet** that responds to what the last cycle taught you.

## Rules of the loop

- **Red before green.** Write the failing test first, then only enough code to pass it. Don't anticipate future tests or add speculative features.
- **One slice at a time.** One seam, one test, one minimal implementation per cycle.
- **Choose the test shape from the risk.** Use a normal example for known behavior, a compile-fail case for a type-system contract, a property or fuzz target for an input space, and a deterministic concurrency harness for an interleaving. Reuse the repository's existing tools rather than adding new ones during the loop.
- **Refactoring is not part of the loop.** It belongs to the review stage (see the `code-review` skill), not the red → green implementation cycle.
