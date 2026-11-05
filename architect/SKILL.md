---
name: architect
description: "Use this skill when the user asks to generate a handoff or blueprint — a compressed implementation spec written to handoffs/ for a local LLM builder to execute. Trigger on phrases like 'create the handoff', 'generate the blueprint', 'package this for the builder', or any request to finalize decisions into an executable spec."
---

# Instructions
Package the conversation's final decisions into execution blueprints under `handoffs/` (workspace root, create if missing) for a smaller local builder model that has NOT seen this conversation. Display each new handoff in chat for review.

One handoff = one phase: a single coherent concern, aim under ~250 lines. Each phase is its own file `handoffs/NN-<slug>.md`, numbered sequentially in execution order with two digits (01, 02, …). To insert a phase between existing ones, append a letter to the earlier number: `01a-<slug>.md` runs after `01` and before `02` — never renumber existing files. Before writing, `ls handoffs/` and continue from the highest existing number. Files are never renamed or promoted; the builder finds the next pending one by sort order. Never modify a file whose first line is `STATUS: done`.

## The builder writes the code
Specify WHAT must be true, not HOW to implement it.
- **CONTRACT** (default for new/changed functions): exact signature, one-line docstring, behavior bullets stating observable outcomes (inputs→outputs, edge cases, error behavior), and `Mirror:` an existing function to imitate. No pseudocode — if a bullet reads like a line of code, delete it and add a verification assertion instead.
- **EDIT**: anchored one-sentence instruction for small changes (imports, dict keys, wiring, UI tweaks, docs).
- **VERBATIM**: exact code, ONLY when (a) the code is shorter than any prose spec of it (a regex, a constant) or (b) no offline check can catch drift (LLM prompt strings). If you can test it, contract it.

## Format
- Line 1 is exactly `STATUS: pending` — plain text, no frontmatter fences. The builder flips it to `STATUS: done` when the build passes.
- Then sections in order, XML tags as delimiters, no markdown fences inside: `<objective>` (1-2 sentences, the builder's ambiguity tiebreaker), `<target_files>` (full paths, CREATE/MODIFY), `<constraints>` (checkable invariants, untouchable files/APIs), `<state_changes>` (`FILE:` markers, tier-labeled items), `<imperative_steps>` (numbered order), `<verification>`, optional `<roadmap>` (one line naming later-phase files so the builder knows they are out of scope).
- Anchor edits to code landmarks, never line numbers. Never compress identifiers, paths, or signatures.
- `<verification>` is the contract enforcement: copy-paste-runnable, deterministic, offline (stub models/servers inline in the snippet), compile/build check first, at least one assertion per CONTRACT, expected output stated.
