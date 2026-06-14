# agent-skills

Personal agent skills for Claude Code + Codex, distributed with the
[`skills`](https://github.com/mattpocock/skills) CLI.

## Install (global, both agents)

One command. Installs every skill into `~/.claude/skills/` and `~/.codex/skills/`,
so every repo and both agents see them — no per-repo setup.

```bash
bunx skills@latest add Andrew-Pynch/agent-skills --all -g -a claude-code -a codex -y
```

Interactive instead (pick skills + agents + scope):

```bash
bunx skills@latest add Andrew-Pynch/agent-skills
```

## Update

After editing a skill here and pushing:

```bash
bunx skills@latest update -g
```

## Skills

| Skill | What it does |
| --- | --- |
| `inspect-package-source` | Anti-hallucination rail: read a dependency's real source via `opensrc` instead of guessing its API. |

## Adding a skill

Create `skills/<name>/SKILL.md` with frontmatter (`name`, `description`), commit,
push. The `description` is the trigger surface the model matches against — write
it as the moment the skill should fire. Re-run the update command above to pull
it everywhere.
