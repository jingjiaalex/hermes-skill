# Hermes Skills

[Hermes Agent](https://github.com/NousResearch/hermes-agent) 相关的 Claude Code Skills 合集。一行命令装到本地，在 Claude Code 里用自然语言触发。

## Skills 列表

| Skill | 说明 | 一键安装 |
|-------|------|---------|
| [hermes-setup](./hermes-setup/) | 一键配置 Hermes Agent（本地 + 服务器） | 见下方 |

后续会加更多 Skill，Star 一下不迷路。

---

## hermes-setup：一键配置 Hermes Agent

### 装法

```bash
mkdir -p ~/.claude/skills/hermes-setup
curl -fsSL https://raw.githubusercontent.com/vortwang/hermes-skill/main/hermes-setup/SKILL.md \
  -o ~/.claude/skills/hermes-setup/SKILL.md
```

装完在 Claude Code 里说：

> 帮我配 Hermes Agent

或者：

> setup hermes

它会问你几个问题（API Key、想用什么模型、要不要接 Telegram），然后自动搞定。

**部署到服务器？** 告诉它 SSH 连接信息，远程装、远程配、自动建 systemd 服务。

### 支持的 Provider

| Provider | 说明 |
|----------|------|
| OpenRouter | 默认选项，美元信用卡 |
| Anthropic | Claude 官方 API |
| OpenAI | GPT 官方 API |
| 国内中转站 | CCSub、DMXAPI 等，微信支付宝 |
| 本地模型 | Ollama、LM Studio |
| 自定义端点 | 任意 OpenAI 兼容 API |

### 它干了什么

**本地部署：**
1. 检测有没有装 Hermes，没有就装
2. 根据你选的 provider 生成 `~/.hermes/config.yaml`
3. 写 API Key 到 `~/.hermes/.env`
4. 配了 Telegram 自动检测本地代理（7897/7890/1087）
5. 跑 `hermes doctor` 验证
6. 启动（macOS 用 launchd 开机自启）

**服务器部署（SSH）：**
1. 通过 SSH 远程安装 Hermes
2. 远程写 config.yaml 和 .env
3. 创建 systemd 服务（hermes + hermes-gateway）
4. 开机自启，后台常驻
5. 日志：`journalctl -u hermes -f`

### 常见坑

- `hermes` 找不到 → `export PATH="$HOME/.hermes/bin:$PATH"`
- Telegram 连不上 → 国内需要代理
- GPT 模型名 → 写 `gpt-5.4` 不带 `openai/` 前缀
- .env 不加引号 → `KEY=sk-xxx` 不是 `KEY="sk-xxx"`
- 服务器报错 → `journalctl -u hermes -e` 看日志

---

## License

MIT
