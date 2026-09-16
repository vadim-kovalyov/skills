---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project issue tracker: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

   If the change touches Rust or Cargo code, or crosses an unsafe, FFI, concurrency, async, operating-system, embedded, compatibility, or performance-sensitive boundary, call the Skill tool with "rust-systems-engineering". Carry the applicable contracts and validation scope into the spec.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the spec using the template below, then publish it to the project issue tracker. Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## Behavioral Requirements

An extensive numbered list of externally meaningful behavior.

For user-facing work, use user stories:

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

For libraries, protocols, infrastructure, or systems code where a user-story sentence would be artificial, state caller capabilities and invariants directly:

1. A caller can submit a truncated frame and receive a typed error without consuming bytes from the next frame.
2. Cancellation after the temporary file is flushed leaves either the old file or the complete new file visible, never a partial file.

Cover normal behavior, invalid input, failure, cancellation, shutdown, resource limits, and compatibility where they apply.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions
- Ownership and lifetime contracts
- Error and panic behavior
- Concurrency, cancellation, and shutdown behavior
- Supported targets, features, MSRV, serialization, and ABI constraints
- Performance budgets or resource limits that callers depend on

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)
- Relevant feature combinations, targets, compile-fail cases, properties, fuzz targets, concurrency harnesses, unsafe checks, or benchmarks already supported by the repository

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>
