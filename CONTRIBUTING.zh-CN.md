# Contributing

[English](CONTRIBUTING.md)

欢迎提交新的 AI agent skill、改进现有 skill 或补充文档。

## 基本规范

1. 每个正式 skill 放在 `skills/<skill-name>/`。
2. 每个 skill 必须包含 `SKILL.md`。
3. skill 内容应使用英文编写。
4. `SKILL.md` 必须包含 frontmatter：

   ```yaml
   ---
   name: my-skill
   description: Explain what this skill does and when to use it.
   ---
   ```

5. skill 名称使用小写字母、数字和连字符，例如 `browser-automation`。
6. 如果包含脚本，请说明运行环境、依赖安装方式和示例命令。
7. 长文档放入 `references/`，模板或示例资源放入 `assets/`。

## 安全与隐私

- 不要提交 `.env`、API key、私钥、cookie、会话日志或任何敏感数据。
- skill 可能会指导 agent 执行命令；提交前请确认脚本和指令安全、可审计。
- 如需网络访问、文件删除、交易/支付等高风险操作，请在 skill 中明确说明确认步骤。

## 建议流程

```bash
git checkout -b add-my-skill
git add skills/my-skill README.md CONTRIBUTING.md
git commit -m "Add my-skill"
```

提交前请自查：

- frontmatter 是否完整。
- 路径是否均相对于 skill 目录。
- README 或 skill 文档是否能让第一次使用的人快速上手。
- 没有提交本地生成文件、缓存或敏感信息。
