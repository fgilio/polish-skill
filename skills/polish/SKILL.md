---
name: polish
description: >
  Polish a codebase by reviewing it in search of simplifications,
  unifications, and reductions in cognitive load.
  Use when: simplifying code, reducing indirection, hunting for
  maintainability wins, reviewing changes/a branch/a project for clarity.
  Invoke only when the user explicitly asks to polish or simplify (e.g.
  /polish) or when an orchestrating skill delegates to it. Do not
  auto-invoke it opportunistically.
argument-hint: "[--interactive] [--scope=changes|branch|project]"
user-invocable: true
disable-model-invocation: false
---

# Polish

Review code in search of simplification. The bar is **semantic clarity**: the code should be crystal clear six months from now, crystal clear while debugging a production bug under pressure, and conceptually clear enough that adding features is obvious. Favor simplifications, unifications, and anything that decreases cognitive load. Avoid overengineering. Eliminate indirection.

Load the required `coding` skill before reviewing (`coding:coding` in a plugin installation). If it is unavailable, report the missing prerequisite and link to its [installation instructions](https://github.com/fgilio/coding-skill#installation) instead of running an incomplete review.

## Parsing arguments

`$ARGUMENTS` carries the invocation. Resolve two things from it:

1. **Mode.** If it contains `--interactive`, `-i`, or the bare word `interactive`, run in **interactive mode**. Otherwise run in **report mode** (the default).
2. **Scope.** Use the value of `--scope=…` if present: `changes` (the uncommitted diff), `branch` (the current branch's diff against its base), or `project` (the entire codebase). Accept the spelled-out forms too (`the current changes`, `the current branch`, `the entire project`). Otherwise infer the most useful scope from context — default to `changes` when the repo has uncommitted work, else `branch`, else ask the user only if genuinely ambiguous.

State the resolved mode and scope in one line before you begin, so the choice is visible and correctable.

## The review (both modes)

Analyze the scope in depth, fully end to end, every file in it. When the scope is the entire project and too large for a single pass, shard it: give each persona several subagents, one per slice of the tree, rather than letting one subagent skim everything and call it coverage. State in the synthesis what was read in full and what was only surveyed.

Convene the panel. Spawn **parallel subagents**, one per persona, each reviewing the same scope through that person's lens. Otwell, DHH, and Wathan always sit on the panel; Porzio joins only when the scope warrants it (see his entry):

- **Taylor Otwell — does it feel Laravel-native?** Hunts for raw PHP doing what the framework already does: hand-rolled loops instead of collections, string fiddling instead of `Str`, manual guards instead of validation rules or policies, queries that belong in scopes, bootstrapping that fights the framework. The bar is code that reads as if it shipped with Laravel itself. In non-Laravel codebases, applies the same lens to that stack's framework idioms.
- **DHH — is it simple and easy to maintain?** Hunts for needless abstraction and ceremony: layers that exist "just in case", speculative flexibility, patterns imported without earning their keep, three concepts where one would do. Prefers deleting code over organizing it. The bar is code a maintainer can change confidently months from now without archaeology.
- **Adam Wathan — does it speak the language of the system?** Hunts for semantic drift: names that don't match the domain vocabulary, one-off solutions where the codebase already has an established pattern, near-duplicates that should collapse into an existing convention. The bar is code that looks like it was written by the same hand as the rest of the system.
- **Caleb Porzio — is the frontend built the way we build frontend?** Owns everything Livewire, Flux, and Alpine: component boundaries and communication, state living where it belongs (server-side vs Alpine), Flux components over bespoke markup, a sprinkle of Alpine over a heavyweight solution. The bar is a frontend a backend developer can follow. Spawn this persona only when the scope contains relevant files (Livewire components, Blade views, Alpine or other frontend code); skip it otherwise and note the skip in the synthesis.

The panel also flags **comment and prose noise**, not only code: comments that restate a documented convention, narrate what the next line plainly does, recount history (biography), or sit in a header block when they explain one specific line. Apply the `coding` skill's comment rules as part of the review.

The panel also **traces related interfaces** when a change introduces or alters an interface, data representation, or responsibility. Apply the `coding` skill's Coherent Interfaces section to the affected flow. Wathan owns the trace, DHH challenges added abstractions, and Otwell or Porzio checks framework and component ownership. Use the existing panel, not a separate review round.

Follow dependencies far enough to establish the contract without turning a branch review into a project-wide redesign. Each traced flow returns either a concrete proposal or a short no-change verdict with the reason. Do not force a finding or pad the final report with no-change verdicts.

Each subagent returns concrete, file-specific findings — not platitudes. Each finding should name the file/symbol, the problem (what costs clarity or adds cognitive load), the proposed simplification, affected consumers, and any distinct behavior or external contracts that must stay unchanged.

Synthesize the panel's reviews into a single deduplicated, prioritized list. Lead with the highest-leverage simplifications. Call out anything where the personas disagree and say which way you lean and why.

Reconcile related naming and ownership proposals into one consistent design before presenting them. Show the final vocabulary together, not several conflicting local renames. Distinguish correctness defects from design proposals, and state when a proposal would change behavior instead of treating it as mechanical cleanup.

## Report mode (default)

Present the synthesized findings. **Do not write any code.** End with the prioritized list so the user can pick what to pursue next.

## Interactive mode (`--interactive`)

After presenting the synthesized findings, use **AskUserQuestion** to validate/confirm each point with the user — one decision at a time, grouped sensibly. Assemble the confirmed points into a plan together, then **implement the agreed plan**. Skip anything the user declines.
