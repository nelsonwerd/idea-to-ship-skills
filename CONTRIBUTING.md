# Contributing to idea-to-ship

Thanks for your interest. This repo is the **source of truth** for the idea-to-ship Agent Skills — six composable skills across two tiers (**manual:** `ideate` · `deep-dive` · `prompt-pack`; **autonomous:** `autopilot` · `build-loop` · `audit-and-fix`). They're *prose skills that ride an agent's existing tools*, not bespoke software — so contributing is mostly careful writing, not coding.

## Repo layout

- `skills/<name>/` — each skill: a `SKILL.md` plus a `references/` folder. **This is what you edit.**
- `<name>.skill` — the packaged zip for each skill, built by `scripts/package.sh`. Don't hand-edit it.
- `.claude-plugin/` — the Claude Code plugin manifests.
- `README.md` — the suite docs.

## Making a change

1. Edit the skill under `skills/<name>/` (the `SKILL.md` and/or files in `references/`).
2. Rebuild the packaged zip(s):
   - `scripts/package.sh <name>` — one skill (validates frontmatter + rebuilds its zip)
   - `scripts/package.sh` — all skills
3. Open a PR. Keep the diff focused — one skill / one concern per PR where you can.

## Skill style (match the existing voice)

- Terse, operational, em-dash-heavy; **progressive disclosure** — keep `SKILL.md` lean and push depth into `references/`.
- **No emoji in skill files.** (The README may use them; the skills don't.)
- `name:` in frontmatter must equal the folder name, unquoted.
- Keep `SKILL.md` well under 500 lines.
- The `description:` is third-person-dominant, under 1024 characters, with sharp trigger phrases **and** a "do NOT use this for…".
- **Honest-by-construction** is the whole ethos: every skill states its *own* limits in its text. Don't let a skill oversell itself.

## Testing

There's no test suite for the suite itself — verification is `scripts/package.sh` passing (frontmatter validates, zips rebuild) plus content review. For a behavior change, describe in the PR how you exercised it.

## Reporting issues / requesting skills

Use the issue templates — a skill misbehaving, or an idea for a new/improved skill. Concrete examples (what you typed, what you expected, what you got, and where you ran it) make things fixable fast.

## License

By contributing, you agree your contributions are licensed under the repo's [MIT License](LICENSE).
