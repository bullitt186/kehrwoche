# Scope

What this project is for, so contributions go in the same direction. Changing this file is a
maintainer decision, recorded as an ADR.

## Purpose

Agent Skills accumulate explanations, duplicate examples and boilerplate that cost tokens every
time a skill loads. Kehrwoche is a prompt for a coding agent that audits a skills directory and
reduces that cost without dropping behaviour the skills rely on. It is for people who write and
maintain their own skills (`README.md`).

## In scope

- The optimization prompt (`skill-optimization-prompt.md`): inventory, description (routing)
  optimization, classification of the body into core rules, background, examples, templates and
  redundant content, moving non-core content into on-demand reference files, faithfulness check.
- Example reference files that show the target format (`references/`).
- Documentation of usage and of the sources the rules are based on (`README.md`).

## Out of scope

- Removing content only to save tokens: the faithfulness check keeps everything load-bearing
  (`README.md`, "Philosophy").
- Applying changes without the user's review: the prompt hands back a before/after table and
  waits for confirmation (`README.md`, "Usage").
- A tool, script or package that runs the optimization: Open: to be decided by the maintainer.
- Formats other than Agent Skills (`SKILL.md` with `references/`, `scripts/`, `assets/`): Open:
  to be decided by the maintainer.

## Direction

Open: to be decided by the maintainer. There is no roadmap file or open issue yet.

## Principles

- Keep what earns its keep, in full; cut what does not ("Sparsam, not stingy").
- Every moved piece gets a labelled place and a pointer at the step that needs it.
- Descriptions state what a skill does and when to use it, nothing more.
- Rules are grounded in the named sources (`README.md`, "Source").
