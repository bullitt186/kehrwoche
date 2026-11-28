# Skill Optimization Prompt for Claude Code

You are auditing and optimizing a set of Agent Skills (SKILL.md files, optionally with `references/`, `scripts/`, `assets/` subdirectories) for token efficiency, without degrading functional quality.

Apply the following rules, in this order. Do not skip a step to save time — token savings that break routing or silently drop required behavior are worse than no optimization.

## Step 0: Inventory

List every skill directory found. For each, report: name, description length (tokens), body length (lines/tokens), presence of `references/`, `scripts/`, `assets/`, and total bundled reference size. Flag any skill with:
- No description, or a description under ~15 words
- A body over 500 lines
- Reference files that duplicate content already in the body
- A description that reads like a feature list rather than a trigger condition
- Body content addressing hypothetical or future scenarios with no evidence the skill's actual use ever hits them (YAGNI candidates)
- Body prose that is verbose, hedging, or jargon-heavy where a terser rewrite would lose no information (Caveman candidates)

## Step 1: Description (routing layer) optimization

For each skill's YAML frontmatter `description`:
- Rewrite to the **minimal set of claims that distinguishes this skill from every other skill in the directory** — not a feature list, not a synonym dump. Test each clause: "if I remove this, would a request that should trigger this skill instead go unmatched or match the wrong skill?" If removing it changes nothing, cut it.
- If a description is missing or too generic to route reliably, generate one from the skill's actual body content: name the primary capability, the concrete trigger condition, and any unique identifiers (tool names, file types, frameworks, commands) — not the general topic area. Bad: "Helps with documents." Good: "Use when filling PDF forms or extracting table data from PDFs."
- Do not exceed what's needed for disambiguation. If only one skill in the directory handles PDFs, the description doesn't need every PDF-related keyword — only enough to fire reliably.
- After rewriting, simulate 3–5 realistic user requests that should and should not trigger this skill, and check the new description against them mentally before finalizing.

## Step 2: Body classification and restructuring

For each skill body, classify every paragraph/section into one of six types:
- **Core rule** — actionable instructions the agent must follow to complete the task correctly. Keep in SKILL.md, always loaded.
- **Background** — rationale, explanation, "why this works." Move to `references/background.md`.
- **Example** — illustrative code/I-O samples. Keep only the single most representative example per distinct concept; move the rest (or all, if the pattern is explained clearly in prose) to `references/examples.md`. Strip redundant near-duplicate examples.
- **Template** — boilerplate/copy-paste scaffolding. Move to `references/templates.md` or `assets/`.
- **Redundant** — repeated or restated content adding no new information. Delete.
- **Speculative (YAGNI)** — content addressing hypothetical future needs, unused configuration options, "just in case" flexibility, or edge cases with no evidence the skill has ever actually hit them. Delete. Distinct from Redundant: this content can be novel and well-written — the issue isn't restatement, it's that nothing in the skill's real use justifies carrying it. Test: would deleting this leave the skill unable to handle a task it has actually been asked to do? If the honest answer is "no, but it might someday," cut it — don't hedge by keeping it "just in case."

Critical exception — do not move to a reference file: **any example that is the only place a required format, schema, or behavior is actually specified.** If no core rule states the rule explicitly in prose, the example IS the specification and must stay in the always-loaded body. When in doubt, keep it in the core.

**Caveman rewrite pass** — after classifying, tighten every Core rule paragraph that survives: short sentences, no filler adjectives ("robust," "comprehensive," "seamless"), no hedging ("might," "could potentially," "in most cases" when the rule is actually absolute), no throat-clearing before the instruction — state the rule, not the reason it's nice to have. This is a rewrite, not a cut: every fact, number, and threshold the original stated must still be present afterward (checked in Step 5) — only the wrapping prose gets leaner.

For every content type that gets moved out of SKILL.md into a reference file, add a pointer in the body at the exact step where the agent would need it, e.g.:

```
## Step 3: Generate the client code
Before writing code, read `references/code-generation-rules.md` for the full rule set.
```

Do not just link references in a "see also" pile at the end — place the pointer at the point of use so Claude only loads it when the task actually reaches that step.

## Step 3: Reference file deduplication

For skills with existing `references/` or bundled files:
- Diff each reference file's content against the (now-restructured) SKILL.md body. Remove anything already covered in the core.
- If a reference file shrinks below ~30 tokens of unique content after dedup, delete it entirely and fold anything essential into the body.
- Add a one-line "when to read this" note at the top of each surviving reference file, plus 3–5 topic keywords, so the agent can decide whether to load it without reading it first.

## Step 4: Hard limits

- Keep the SKILL.md body under 500 lines. If restructuring per Step 2 doesn't get you there, split further — the goal is that everything left in the always-loaded body is a core rule, not padding.
- One skill, one job. If a skill body covers clearly distinct workflows that don't share triggers, propose splitting it into separate skills with separate descriptions rather than compressing it in place — call this out explicitly rather than silently merging unrelated content into one lean file.

## Step 5: Faithfulness check (do not skip)

Before finalizing each skill, compare the original body against the new core + references combined:
- List every operational concept, constraint, number, threshold, or API detail from the original.
- Confirm each one still appears somewhere (core or reference) — nothing may silently disappear, only move or get deleted if it was genuinely redundant/non-actionable.
- If you're unsure whether something was safe to cut, don't cut it — leave it in the core and flag it in your summary instead of guessing.
- This applies equally to content cut under the Speculative/YAGNI classification in Step 2 — if there's any real chance the scenario isn't hypothetical, don't cut it; leave it in the core and flag it instead.

## Step 6: Report

For each optimized skill, output a before/after table:

| Skill | Description (before → after tokens) | Body (before → after lines/tokens) | References created/deduped | Flags |

Flags = anything you weren't fully confident about (e.g. "example moved to reference/examples.md but may implicitly specify required behavior — please verify"), any skill you recommend splitting, and any skill you left untouched because it was already lean or because compressing it risked losing required behavior.

Do not apply changes silently — show me the before/after for each skill and wait for confirmation before overwriting files, unless I've told you to apply everything automatically.