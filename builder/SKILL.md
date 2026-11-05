---
name: builder
description: "Use this skill when executing a handoff blueprint from handoffs/ — reading the spec and generating the code it describes. Trigger on phrases like 'execute the handoff', 'build the next handoff', 'run the blueprint', or whenever handoffs/ contains a pending file and the user asks to implement it."
---

# Instructions
Execute a pre-approved blueprint from `handoffs/` at the workspace root: the file the user names, otherwise the first `handoffs/*.md` in sort order whose first line is `STATUS: pending` (`grep -l '^STATUS: pending' handoffs/*.md | sort | head -1`; a letter suffix like `01a` runs after `01`, before `02`). If none exists, stop and report — do not improvise a plan. Do not re-litigate design decisions; your judgment goes into writing good code WITHIN the contracts, never into changing them.

## Rules
1. Read `<objective>` (your ambiguity tiebreaker) and `<constraints>` (hard limits) before any edit. Verify `<target_files>` paths: MODIFY must exist, CREATE must not.
2. Follow `<imperative_steps>` in order. Locate edits by code landmarks, never by counting lines. Touch only listed files; no unrequested improvements. Files named in `<roadmap>` are later phases — out of scope.
3. Tiers in `<state_changes>`:
   - **VERBATIM**: copy character-for-character (an unlabeled code block is VERBATIM).
   - **CONTRACT**: read the target file and the `Mirror:` reference first, then write the body yourself — exact signature, every behavior bullet honored, local style, nothing the bullets don't ask for. Details the bullets leave open are yours; note each for the report.
   - **EDIT**: the smallest change satisfying the instruction, in local style.
4. Run `<verification>` top to bottom. On failure: fix your own code and re-run — never alter verification commands, expected outputs, or VERBATIM blocks to make a check pass. Max 3 repair attempts per check, then stop and report the failure verbatim.
5. If a landmark is missing or a change clashes structurally with existing code: stop, name the step and file, describe the mismatch in a sentence, list what's done. Never guess past a conflict.
6. Close-out, ONLY when every verification check passed: flip the handoff's first line to `STATUS: done` and append a `<report>` section (files changed; decisions the contracts left open and how you resolved them; every verification result verbatim). On any failure or stop, leave the handoff file untouched and pending — report in chat only.

## Report
Give the same report in chat: files changed; decisions the contracts left open and how you resolved them; every verification result verbatim, pass and fail. A weakened or skipped check is a failed build regardless of output.
