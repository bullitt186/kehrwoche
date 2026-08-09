# Kehrwoche

*"Kehrwoche"* is the Swabian institution of the rotating sweeping duty — every household takes its turn keeping the shared stairwell and sidewalk spotless, on schedule, no exceptions. Nothing fancy, no debate about whether it's necessary. It just gets done, and everyone benefits from it.

This repo applies the same principle to Claude Skills. Most SKILL.md files accumulate the same kind of clutter a stairwell does: leftover explanations nobody reads twice, three examples where one would do, boilerplate that got copy-pasted and never trimmed. It doesn't cost anything to look at — but it costs tokens every single time the skill loads. A proper Swabian doesn't stand for that.

## What's in here

- **`skill-optimization-prompt.md`** — hand this to Claude Code (or any capable agent) pointed at your skills directory. It classifies every skill body into core rules, background, examples, templates, and redundant filler; keeps only the core always-loaded; pushes the rest into `references/` files loaded on demand; and tightens routing descriptions to the minimum needed to trigger reliably. It does not throw things out just to look busy — a faithfulness check makes sure nothing load-bearing goes missing along the way.
- **`references/code-generation-rules.md`** — an example of what a properly swept-out reference file looks like: on-demand, topic-tagged, and only pulled in when the task actually needs it.

## Philosophy

- **Sparsam, not stingy.** Cut what's not earning its keep. Keep what is, in full.
- **A place for everything.** Core rules stay in the always-loaded body. Everything else moves to a labeled reference file with a clear "when to read this" — no dumping ground, no guesswork.
- **No showing off.** A skill's description states exactly what it does and when to use it. No feature-list bragging, no filler adjectives.
- **Check your work.** Nothing gets swept away without confirming it wasn't holding something up. Sweep first, then double-check the corners.

## Usage

1. Point Claude Code at your skills directory.
2. Paste in `skill-optimization-prompt.md`.
3. Review the before/after table it hands back.
4. Confirm, then let it sweep.

No skill left behind — just none of them carrying more than they should.

## Source

The optimization rules in `skill-optimization-prompt.md` are based on the taxonomy and methodology described in:

> Y. Gao, Z. Li, Y. Yuan, Z. Ji, P. Ma, and S. Wang, "SkillReducer: Optimizing LLM Agent Skills for Token Efficiency," *arXiv preprint arXiv:2603.29919*, 2026. [https://arxiv.org/abs/2603.29919](https://arxiv.org/abs/2603.29919)

Combined with Anthropic's own progressive-disclosure guidance for Agent Skills: [https://docs.claude.com/en/docs/agents-and-tools/agent-skills/best-practices](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/best-practices)