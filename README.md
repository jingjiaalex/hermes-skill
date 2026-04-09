# Hermes Agent 一键配置 Skill

Claude Code Skill，一句话把 [Hermes Agent](https://github.com/NousResearch/hermes-agent) 配好。

不用翻文档，不用手动改 YAML。告诉它你有什么 Key，剩下的它来。

## 装法

```bash
# 把 SKILL.md 丢到 Claude Code 的 skills 目录
mkdir -p ~/.claude/skills/hermes-setup
curl -fsSL https://raw.githubusercontent.com/vortwang/hermes-setup-skill/main/SKILL.md \
  -o ~/.claude/skills/hermes-setup/SKILL.md
```

装完之后在 Claude Code 里说一句：

> 帮我配 Hermes Agent

或者：

> setup hermes

它会问你几个问题（API Key、想用什么模型、要不要接 Telegram），然后自动搞定。

## 支持的 Provider

| Provider | 说明 |
|----------|------|
| OpenRouter | 默认选项，美元信用卡 |
| Anthropic | Claude 官方 API |
| OpenAI | GPT 官方 API |
| 国内中转站 | CCSub、DMXAPI 等，微信支付宝 |
| 本地模型 | Ollama、LM Studio |
| 自定义端点 | 任意 OpenAI 兼容 API |

## 它干了什么

1. 检测有没有装 Hermes，没有就装
2. 根据你选的 provider 生成 `~/.hermes/config.yaml`
3. 写 API Key 到 `~/.hermes/.env`
4. 配了 Telegram 的话自动检测本地代理（7897/7890/1087）
5. 跑 `hermes doctor` 验证
6. 启动

## 常见坑

- **hermes 命令找不到**：`export PATH="$HOME/.hermes/bin:$PATH"`
- **Telegram 连不上**：国内需要代理
- **GPT 模型名**：走 custom 端点写 `gpt-5.4` 不是 `openai/gpt-5.4`
- **YAML 缩进**：空格不是 Tab
- **.env 不加引号**：`KEY=sk-xxx` 不是 `KEY="sk-xxx"`

## License

MIT
