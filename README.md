# harness-engineering

`harness-engineering` is a minimal, portable Agent Skill for making repositories easier for coding agents to navigate, plan, verify, and hand off.

The skill is written against the open Agent Skills format so the same `SKILL.md` can be used in multiple compatible clients, including OpenAI Codex and Claude Code.

## Repository Layout

```text
harness-engineering/
├── README.md
├── .gitignore
└── harness-engineering/
    └── SKILL.md
```

## Install

Copy the `harness-engineering/` skill directory into the location your client scans for skills.

### Codex

Personal install:

```bash
mkdir -p ~/.codex/skills
cp -R harness-engineering ~/.codex/skills/
```

Project install:

```bash
mkdir -p /path/to/repo/.agents/skills
cp -R harness-engineering /path/to/repo/.agents/skills/
```

Codex also supports repository-local discovery from `.agents/skills`. See the official docs for current details.

### Claude Code

Personal install:

```bash
mkdir -p ~/.claude/skills
cp -R harness-engineering ~/.claude/skills/
```

Project install:

```bash
mkdir -p /path/to/repo/.claude/skills
cp -R harness-engineering /path/to/repo/.claude/skills/
```

Claude Code discovers skills from `.claude/skills` and personal skills from `~/.claude/skills`.

## Verify

After installing:

- In Codex, ask what skills are available or trigger a task that should match `harness-engineering`.
- In Claude Code, ask what skills are available or trigger a task that should match `harness-engineering`.

If the skill does not appear immediately, restart the client.

## Design Notes

- Uses only the open `SKILL.md` format and standard frontmatter fields.
- Does not include Codex-only `agents/openai.yaml`.
- Does not include Claude-only extensions such as invocation control fields.
- Keeps the repository intentionally small: one skill, no bundled scripts, no extra references.

## Compatibility

This repository follows the open Agent Skills format:

- Agent Skills quickstart: https://agentskills.io/skill-creation/quickstart
- Agent Skills specification: https://agentskills.io/specification
- Codex skills docs: https://developers.openai.com/codex/skills
- Claude Code skills docs: https://code.claude.com/docs/en/skills

## License

This repository is licensed under the MIT License.
