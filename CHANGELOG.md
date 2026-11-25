# Changelog

All notable changes are documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versions: [SemVer](https://semver.org/).

## [Unreleased]

### Contributors

- CI lints the Markdown with markdownlint-cli2 (`npm run lint`, also the agent stop hook);
  the few findings (missing blank lines before lists, trailing newlines, a code block language)
  are fixed. Nothing changes in the prompt's instructions.
- One setup for coding agents and people ([repo-kit](https://github.com/bullitt186/repo-kit)):
  `AGENTS.md` is the single instruction file (`CLAUDE.md` imports it), `CONTRIBUTING.md` holds
  the Definition of Done every pull request is reviewed against, `docs/SCOPE.md` states purpose
  and non-goals, architecture decisions go to `docs/adr/`. New: pull request template, issue
  forms, `PR hygiene` and `Security` checks, weekly Dependabot updates for GitHub Actions.
