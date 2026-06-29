# AI Agent Skills

[English](README.md)

这个仓库用于开发、整理和分享可复用的 AI agent skills。仓库中的 skill 默认使用英文编写，以便更容易在不同 agent harness 和团队之间共享。

每个 skill 都应该是一个自包含目录，核心入口文件为 `SKILL.md`。skill 应尽量遵循 [Agent Skills](https://agentskills.io/) 约定，并避免绑定到某个特定 agent 实现。

## 仓库结构

```text
.
├── skills/                    # 正式 skill
│   └── <skill-name>/
│       ├── SKILL.md           # 必需：frontmatter + 英文说明
│       ├── scripts/           # 可选：辅助脚本
│       ├── references/        # 可选：长文档/参考资料
│       └── assets/            # 可选：模板、示例数据等资源
├── templates/
│   └── SKILL.template.md      # 新 skill 模板，故意不命名为 SKILL.md
├── README.md                  # 英文文档，默认入口
├── README.zh-CN.md            # 中文文档
├── CONTRIBUTING.md            # 英文贡献指南，默认入口
└── CONTRIBUTING.zh-CN.md      # 中文贡献指南
```

> 不要在 `templates/` 下放名为 `SKILL.md` 的模板文件，否则某些 agent harness 可能会把模板误识别为真实 skill。

## 新建一个 skill

```bash
mkdir -p skills/my-skill
cp templates/SKILL.template.md skills/my-skill/SKILL.md
```

然后编辑 `skills/my-skill/SKILL.md` 顶部的 frontmatter：

```yaml
---
name: my-skill
description: Clearly explain what this skill does and when an agent should use it.
---
```

建议：

- skill 指令使用英文编写。
- skill 名称使用小写字母、数字和连字符，例如 `pdf-tools`、`code-review`。
- `description` 要具体，便于 agent 判断何时加载该 skill。
- skill 内引用脚本、文档和资源时，使用相对于 skill 目录的路径。

## 安装 skills

使用 `npx` 调用 `skills` CLI 从本仓库安装 skills：

```bash
# 只列出可用 skills，不安装
npx skills add pidofme/skills --list

# 安装本仓库中的所有 skills
npx skills add pidofme/skills

# 只安装一个 skill
npx skills add pidofme/skills --skill incus-admin
```

具体安装目标取决于你使用的 agent/client 以及 `skills` CLI 选项。如有需要，可以交互式选择 agent，或传入 CLI 支持的对应 `--agent` 选项。

## 发布前检查

- `SKILL.md` 包含 `name` 和 `description` frontmatter。
- `name` 使用小写字母、数字、连字符，且不超过 64 个字符。
- `description` 简明但具体，不超过 1024 个字符。
- skill 指令使用英文编写。
- 辅助脚本有文档说明，并且可安全重复运行。
- 不提交密钥、token、私有数据或本地会话文件。

## License

MIT。详见 [LICENSE](LICENSE)。
