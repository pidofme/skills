# Contributing

[中文贡献指南](CONTRIBUTING.zh-CN.md)

Contributions are welcome: new skills, improvements to existing skills, and documentation updates.

## General rules

1. Put each production-ready skill under `skills/<skill-name>/`.
2. Each skill must include a `SKILL.md` file.
3. Skill content should be written in English.
4. `SKILL.md` must include frontmatter:

   ```yaml
   ---
   name: my-skill
   description: Explain what this skill does and when to use it.
   ---
   ```

5. Use lowercase letters, numbers, and hyphens for skill names, for example `browser-automation`.
6. If a skill includes scripts, document the runtime, dependency setup, and example commands.
7. Put long-form references in `references/`, and reusable assets or templates in `assets/`.

## Security and privacy

- Do not commit `.env` files, API keys, private keys, cookies, session logs, or other sensitive data.
- Skills can instruct agents to run commands; make sure scripts and instructions are safe and auditable.
- For high-risk operations such as network access, deletion, payments, or trades, clearly document confirmation steps in the skill.

## Suggested workflow

```bash
git checkout -b add-my-skill
git add skills/my-skill README.md CONTRIBUTING.md
git commit -m "Add my-skill"
```

Before submitting, check that:

- Frontmatter is complete.
- Paths are relative to the skill directory.
- The documentation helps a first-time user get started quickly.
- No local generated files, caches, or secrets are committed.
