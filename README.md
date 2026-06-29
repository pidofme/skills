# AI Agent Skills

[中文说明](README.zh-CN.md)

This repository is for developing, organizing, and sharing reusable AI agent skills. Skills in this repository should be written in English by default so they can be shared across agent harnesses and teams more easily.

Each skill should be a self-contained directory with a `SKILL.md` entry file. Skills should generally follow the [Agent Skills](https://agentskills.io/) convention and remain compatible with pi.

## Repository layout

```text
.
├── skills/                    # Production-ready skills
│   └── <skill-name>/
│       ├── SKILL.md           # Required: frontmatter + instructions, written in English
│       ├── scripts/           # Optional helper scripts
│       ├── references/        # Optional long-form references
│       └── assets/            # Optional templates, examples, fixtures, etc.
├── templates/
│   └── SKILL.template.md      # Template for new skills; intentionally not named SKILL.md
├── README.md                  # English documentation, default
├── README.zh-CN.md            # Chinese documentation
├── CONTRIBUTING.md            # English contribution guide, default
└── CONTRIBUTING.zh-CN.md      # Chinese contribution guide
```

> Do not put a file named `SKILL.md` under `templates/`, otherwise some agent harnesses may discover the template as a real skill.

## Create a new skill

```bash
mkdir -p skills/my-skill
cp templates/SKILL.template.md skills/my-skill/SKILL.md
```

Then edit the frontmatter at the top of `skills/my-skill/SKILL.md`:

```yaml
---
name: my-skill
description: Clearly explain what this skill does and when an agent should use it.
---
```

Guidelines:

- Write skill instructions in English.
- Use lowercase letters, numbers, and hyphens for skill names, for example `pdf-tools` or `code-review`.
- Make `description` specific; agents use it to decide when to load the skill.
- Use paths relative to the skill directory when referencing scripts, references, and assets.

## Use with pi

Choose one of the following approaches:

```bash
# Load one skill directly
pi --skill /path/to/this-repo/skills/my-skill

# Or copy a skill to the global skill directory
cp -R skills/my-skill ~/.pi/agent/skills/
```

You can also add the skills directory to pi settings:

```json
{
  "skills": ["/path/to/this-repo/skills"]
}
```

## Pre-release checklist

- `SKILL.md` contains `name` and `description` frontmatter.
- `name` uses lowercase letters, numbers, and hyphens, and is at most 64 characters.
- `description` is concise, specific, and at most 1024 characters.
- Skill instructions are written in English.
- Helper scripts are documented and safe to run repeatedly.
- No secrets, tokens, private data, or local session files are committed.

## License

MIT. See [LICENSE](LICENSE).
