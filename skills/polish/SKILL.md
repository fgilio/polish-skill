---
name: polish
description: >
  Polish a codebase by reviewing it in search of simplifications,
  unifications, and reductions in cognitive load. Convenes Taylor Otwell,
  DHH, Adam Wathan, and Caleb Porzio as parallel reviewer personas.
  Runs non-interactively by default (report only); pass --interactive to
  confirm each finding and implement an agreed plan.
  Use when: simplifying code, reducing indirection, hunting for
  maintainability wins, reviewing changes/a branch/a project for clarity.
argument-hint: "[--interactive] [--scope=the current changes|the current branch|the entire project]"
user-invocable: true
disable-model-invocation: true
---

# Polish

Review code in search of simplification. The bar is **semantic clarity**:
the code should be crystal clear six months from now, crystal clear while
debugging a production bug under pressure, and conceptually clear enough
that adding features is obvious. Favor simplifications, unifications, and
anything that decreases cognitive load. Avoid overengineering. Eliminate
indirection.

## Parsing arguments

`$ARGUMENTS` carries the invocation. Resolve two things from it:

1. **Mode.** If it contains `--interactive`, `-i`, or the bare word
   `interactive`, run in **interactive mode**. Otherwise run in
   **report mode** (the default).
2. **Scope.** Use the value of `--scope=…` if present. Otherwise infer the
   most useful scope from context — default to **the current changes**
   (uncommitted diff) when a repo has them, else the current branch's diff
   against its base, else ask the user only if genuinely ambiguous.

State the resolved mode and scope in one line before you begin, so the
choice is visible and correctable.

## The review (both modes)

Analyze the scope in depth, fully end to end, every file in it.

Convene the panel. Spawn **4 parallel subagents**, one per persona, each
reviewing the same scope through that person's lens:

- **Taylor Otwell** — Laravel-idiomatic design, expressive APIs, developer
  ergonomics, "make the right thing the easy thing."
- **DHH** — ruthless simplicity, convention over configuration, deleting
  abstraction, conceptual compression, rejecting needless ceremony.
- **Adam Wathan** — composability, naming, small focused units, refactoring
  toward clarity, utility-first pragmatism.
- **Caleb Porzio** — reducing indirection, removing layers, magic that
  pays for itself, keeping the mental model small.

Each subagent returns concrete, file-specific findings — not platitudes.
Each finding should name the file/symbol, the problem (what costs clarity
or adds cognitive load), and the proposed simplification.

Synthesize the four reviews into a single deduplicated, prioritized list.
Lead with the highest-leverage simplifications. Call out anything where the
personas disagree and say which way you lean and why.

## Report mode (default)

Present the synthesized findings. **Do not write any code.** End with the
prioritized list so the user can pick what to pursue next.

## Interactive mode (`--interactive`)

After presenting the synthesized findings, use **AskUserQuestion** to
validate/confirm each point with the user — one decision at a time, grouped
sensibly. Assemble the confirmed points into a plan together, then
**implement the agreed plan**. Skip anything the user declines.
