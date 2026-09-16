# Systems Prototype

Build the smallest runnable artifact that answers one systems question. Use this branch when the uncertainty is about protocol behavior, concurrency, unsafe assumptions, operating-system interaction, resource lifetime, target behavior, or performance.

Call the Skill tool with "rust-systems-engineering" when the prototype uses Rust or crosses a Rust systems boundary.

## Process

### 1. State the question and verdict

Write one precise question before building. Define the observable result that would support or reject the current design.

Examples:

- Can cancellation leave this file replacement half-applied?
- Does this ownership shape allow a callback to outlive its context?
- Which queue policy preserves bounded memory under overload?
- Is the regression caused by allocations, syscalls, or lock contention?

### 2. Build the smallest faithful harness

Use the repository's existing project layout, dependencies, scripts, and toolchain. Prefer one small binary, test, example, benchmark, simulator, or protocol driver over a miniature production system.

- Keep only the boundary under investigation real.
- Stub unrelated dependencies with fixed data or a local process.
- Make timing, seeds, inputs, target, features, and environment explicit.
- Add no dependency or tool unless the user approves it.
- When unsafe code is part of the question, keep it no broader than required and write its assumed invariant beside it.

### 3. Surface the evidence

Print or capture the state that decides the question: transitions, ownership events, resource counts, trace points, output bytes, timing samples, or failure rates. A prototype that only exits successfully has not answered a systems question.

For performance questions, record a baseline and changed result under the same build profile and environment. For concurrency questions, prefer a deterministic schedule or high-reproduction stress loop over one lucky run.

### 4. Stop at the answer

Do not harden the artifact into production code. Skip broad error handling, general configuration, reusable abstractions, and unrelated tests. Keep only what makes the result repeatable and interpretable.

Capture:

- the question
- the command that runs the prototype
- the observed evidence
- the verdict and remaining uncertainty
- the target, features, operating system, and toolchain that bound the result

Then follow the parent skill's capture process. The production change absorbs the decision, not the prototype's shortcuts.
