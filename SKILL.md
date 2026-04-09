---
name: hermes-setup
description: One-click Hermes Agent setup. Installs Hermes, configures model provider (OpenRouter/CCSub/custom), sets up Telegram bot. Use when user says "setup hermes", "install hermes agent", "configure hermes", or "帮我配hermes".
---

# Hermes Agent 一键配置 Skill

自动完成 Hermes Agent 的安装、模型配置和 Telegram Bot 接入。零经验用户直接运行即可。

## 前置条件

1. **操作系统**：macOS 或 Linux（WSL2 也行）
2. **LLM API Key**：至少有一个能用的 API Key（下面会引导选择）
3. **Telegram Bot Token**（可选）：找 @BotFather 申请
4. **Telegram User ID**（可选）：给 @userinfobot 发消息获取

## 执行流程

### Step 1: 收集信息

问用户以下信息：

```
必填：
- 你有哪个平台的 API Key？
  a) OpenRouter（默认，美元信用卡）
  b) Anthropic 官方（anthropic.com）
  c) OpenAI 官方（openai.com）
  d) 国内中转站（如 CCSub、DMXAPI 等，支持微信/支付宝）
  e) 本地模型（Ollama、LM Studio）
  f) 其他自定义端点

- API Key 是什么？
- 想用什么模型？（根据 provider 推荐默认值）

可选：
- Telegram Bot Token
- Telegram User ID
```

**各 provider 对应配置：**

| 选择 | provider | base_url | 默认模型 | 环境变量 |
|------|----------|----------|---------|---------|
| a) OpenRouter | auto | 不用填 | anthropic/claude-sonnet-4 | OPENROUTER_API_KEY |
| b) Anthropic | anthropic | 不用填 | claude-sonnet-4-6 | ANTHROPIC_API_KEY |
| c) OpenAI | openai | 不用填 | gpt-5.4 | OPENAI_API_KEY |
| d) 国内中转 | custom | 用户提供 | 看中转站支持 | OPENAI_API_KEY |
| e) 本地模型 | custom | http://localhost:11434/v1 | 看本地装了啥 | 不需要 |
| f) 自定义 | custom | 用户提供 | 用户提供 | OPENAI_API_KEY |

如果用户选了 d) 但不知道用哪个中转站，可以提一句：
> 国内比较方便的有 CCSub（ccsub.net，微信支付宝充值）等，选一个注册拿到 Key 就行。

### Step 2: 检查 Hermes 是否已安装

```bash
which hermes || hermes --version
```

如果没安装：

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

安装完重新加载 shell：

```bash
source ~/.bashrc 2>/dev/null; source ~/.zshrc 2>/dev/null
```

验证：
```bash
hermes --version
```

找不到的话：
```bash
export PATH="$HOME/.hermes/bin:$PATH"
hermes --version
```

### Step 3: 写配置文件

**~/.hermes/config.yaml：**

根据用户选的 provider 生成不同配置。

如果是 OpenRouter / Anthropic / OpenAI 官方：
```yaml
model:
  default: 模型名
  provider: provider名
```

如果是 custom（中转站/本地/自定义）：
```yaml
model:
  default: 模型名
  provider: custom
  base_url: 用户提供的URL
```

**~/.hermes/.env：**

```
对应的API_KEY环境变量=用户的Key
```

如果有 Telegram：
```
TELEGRAM_BOT_TOKEN=用户的Token
TELEGRAM_ALLOWED_USERS=用户的ID
TELEGRAM_HOME_CHANNEL=用户的ID
```

**⚠️ 代理配置（国内用户 Telegram 必须）：**

检测本地代理：

```bash
curl -s --connect-timeout 2 -x http://127.0.0.1:7897 https://api.telegram.org > /dev/null 2>&1 && echo "7897" || \
curl -s --connect-timeout 2 -x http://127.0.0.1:7890 https://api.telegram.org > /dev/null 2>&1 && echo "7890" || \
curl -s --connect-timeout 2 -x http://127.0.0.1:1087 https://api.telegram.org > /dev/null 2>&1 && echo "1087" || \
echo "none"
```

找到代理就追加：
```
HTTPS_PROXY=http://127.0.0.1:端口
HTTP_PROXY=http://127.0.0.1:端口
```

没找到就提醒用户 Telegram 需要代理，或跳过 Telegram 直接用 CLI。

### Step 4: 验证

```bash
hermes doctor
```

检查：Model、Base URL、API Key 都显示正确。

### Step 5: 启动

```bash
hermes
```

配了 Telegram 的另开终端：
```bash
hermes gateway
```

### Step 6: 系统服务（可选）

```bash
hermes service install
hermes service start
```

## 常见坑

1. **hermes 命令找不到**：重新加载 shell 或 `export PATH="$HOME/.hermes/bin:$PATH"`
2. **Telegram 连不上**：国内必须配代理（7897/7890/1087）
3. **GPT 模型名**：走 custom 时写 `gpt-5.4` 不是 `openai/gpt-5.4`
4. **Claude 走 custom 也行**：OpenAI 兼容端点同时支持 Claude 模型名
5. **YAML 缩进**：用空格不用 Tab
6. **.env 不加引号**：`KEY=sk-xxx` 不是 `KEY="sk-xxx"`
7. **base_url 末尾**：custom 端点通常要带 `/v1`

## 模型切换

```bash
hermes config set model "模型名"
```

## 完成标志

- [ ] `hermes --version` 有输出
- [ ] `hermes doctor` 无报错
- [ ] `hermes` 能正常对话
- [ ] （可选）Telegram Bot 能收发消息
