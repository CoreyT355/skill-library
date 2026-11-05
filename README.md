# Skill Library

Reusable agent skills for turning implementation decisions into reviewable blueprints and executing them with a separate builder agent.

## Skills

### Architect

[`architect/SKILL.md`](architect/SKILL.md) packages decisions from a conversation into ordered handoff files under `handoffs/`.

Use it after requirements and design choices are settled:

```text
Create the handoff.
Generate a blueprint for these changes.
Package this plan for the builder.
```

Each handoff describes one coherent phase, identifies the files it may touch, records checkable constraints, and provides deterministic verification. The architect specifies observable outcomes rather than implementation details so the builder can write code that fits the target repository.

### Builder

[`builder/SKILL.md`](builder/SKILL.md) executes a handoff without reopening its design decisions.

Use it by naming a handoff or asking for the next pending phase:

```text
Execute handoffs/01-add-search.md.
Build the next handoff.
Run the blueprint.
```

The builder follows the handoff in order, edits only its declared target files, and runs every verification command. A handoff is marked done only after all checks pass.

## Workflow

1. Discuss and finalize the desired behavior with your agent.
2. Invoke the architect to create one or more files in `handoffs/`.
3. Review and approve each generated blueprint.
4. Invoke the builder to execute the first pending handoff.
5. Repeat until all phases are complete.

Handoffs are ordered lexically using names such as:

```text
handoffs/
	01-domain-model.md
	01a-domain-validation.md
	02-api-endpoints.md
```

Every new handoff begins with `STATUS: pending`. After a successful build, the builder changes it to `STATUS: done` and appends a report containing files changed, implementation decisions, and verification results.

## Handoff Vocabulary

Changes in a blueprint use three tiers:

- **CONTRACT** defines a signature and observable behavior while leaving implementation to the builder.
- **EDIT** describes a small, anchored modification such as wiring, imports, or documentation.
- **VERBATIM** supplies exact text when precision matters more than implementation freedom.

The architect prefers contracts that can be enforced by offline, deterministic checks. The builder must not weaken those checks or modify the blueprint to make a failure pass.

## Installation

Place or symlink both skill directories into the location used by your coding agent. The leading dot matters: project directories are `.claude` and `.pi`, not `claude` and `pi`.

| Agent       | Personal, available in all projects        | Project, committed with one repository |
| ----------- | ------------------------------------------ | -------------------------------------- |
| Claude Code | `~/.claude/skills/<skill-name>/SKILL.md`   | `.claude/skills/<skill-name>/SKILL.md` |
| Pi          | `~/.pi/agent/skills/<skill-name>/SKILL.md` | `.pi/skills/<skill-name>/SKILL.md`     |

For example, a project-level Claude Code installation looks like:

```text
.claude/
	skills/
		architect/
			SKILL.md
		builder/
			SKILL.md
```

Pi also discovers the portable Agent Skills locations `~/.agents/skills/` and `.agents/skills/`. Use those when sharing the same standards-compliant skill installation across agents that support them; use Pi's native locations when the skills are specifically for Pi.

Both skills operate on the current workspace regardless of where they are installed. The `handoffs/` directory is created at that workspace's root and is intended to be committed alongside the code when the project benefits from an auditable implementation record.
