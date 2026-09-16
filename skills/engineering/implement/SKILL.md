---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

If the work changes Rust or Cargo code, or crosses an unsafe, FFI, concurrency, async, operating-system, embedded, or performance-sensitive boundary, call the Skill tool with "rust-systems-engineering" before choosing the implementation and validation approach.

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Discover verification commands from repository instructions, manifests, scripts, and CI. Run the fastest relevant compile or static check regularly, targeted tests regularly, and the full repository-defined suite once at the end. For Rust, cover the workspace members, feature combinations, and targets required by the repository rather than assuming the default host build is enough.

Once done, call the Skill tool with "code-review" to review the work.

Commit your work to the current branch.
