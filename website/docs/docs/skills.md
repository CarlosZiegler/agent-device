---
title: Skills
---

# Skills

Skills are structured instruction files (`SKILL.md`) that teach AI coding agents how to use `agent-device` effectively. When an agent loads a skill, it gains domain knowledge about mobile automation workflows, commands, and best practices — so you can say things like "dogfood my app" and the agent knows exactly what to do.

Skills work with [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview), [Codex](https://openai.com/index/codex/), [Cursor](https://www.cursor.com/), [Gemini CLI](https://github.com/google-gemini/gemini-cli), [GitHub Copilot](https://github.com/features/copilot), [Amp](https://amp.dev/), and other AI coding tools that support the skills format.

## Available Skills

| Skill | Description |
|-------|-------------|
| **agent-device** | Core mobile automation: sessions, snapshots, interactions, replay, debugging, and remote tenancy. Router skill that covers the full command surface. |
| **dogfood** | Exploratory QA and bug-hunting: systematically explores a mobile app and produces a structured report with reproduction evidence (screenshots, videos, detailed steps). |

Both skills ship in the [`skills/`](https://github.com/callstackincubator/agent-device/tree/main/skills) directory of this repository.

## Installing Skills

### Via skills.sh

```bash
# Install the core automation skill
npx skills add https://github.com/callstackincubator/agent-device --skill agent-device

# Install the dogfood/QA skill
npx skills add https://github.com/callstackincubator/agent-device --skill dogfood
```

### Via ClawHub

The skills are also available on [ClawHub](https://clawhub.ai/okwasniewski/agent-device).

### From the repository

Clone the repo and the skills are ready to use from the `skills/` directory:

```bash
git clone https://github.com/callstackincubator/agent-device.git

# Skills are at:
# skills/agent-device/SKILL.md
# skills/dogfood/SKILL.md
```

## How Skills Work

Each skill is defined by a `SKILL.md` file with YAML frontmatter:

```yaml
---
name: dogfood
description: Systematically explore and test a mobile app...
allowed-tools: Bash(agent-device:*), Bash(npx agent-device:*)
---
```

- **name** — Identifier used to invoke the skill
- **description** — Tells the AI agent when to activate the skill (trigger phrases like "dogfood", "QA", "test this app")
- **allowed-tools** — Restricts which CLI commands the skill can run

The body of the skill contains workflow instructions, command examples, and references to deeper documentation. When an AI agent loads the skill, it follows the workflow step-by-step.

### Reference Files

Skills can include reference files for deep-dive topics. The agent-device skill includes references for:

- Snapshot refs and targeting
- Logs and debugging
- Session management
- Permissions and security
- Video recording
- Coordinate system
- Batching
- Performance metrics
- Remote tenancy

These are loaded on-demand when the agent needs specific guidance.

## Creating Custom Skills

You can create your own skills following the same pattern. Use the bundled skills as templates:

1. Create a directory under `skills/your-skill-name/`
2. Add a `SKILL.md` with YAML frontmatter and workflow instructions
3. Optionally add `references/` for deep-dive docs and `templates/` for output templates

See the [dogfood skill](https://github.com/callstackincubator/agent-device/tree/main/skills/dogfood) for a complete example with references and templates.
