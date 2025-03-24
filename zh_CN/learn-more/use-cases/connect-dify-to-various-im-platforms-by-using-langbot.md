# 将 Dify 快速接入 QQ、微信、飞书、钉钉、Telegram、Discord 等平台

## 1. 概述

市面上有多种多样的 IM 平台，接入机器人的方法各不相同，使用难度也参差不齐，导致难以快速接入 Dify 等 LLMOps 平台，使用 Dify 的强大生态。而借助 [LangBot](https://github.com/RockChinQ/LangBot)，即可在短时间内将 Dify 接入到 QQ、微信、飞书、钉钉、Telegram、Discord 等平台提供服务。同时，LangBot 还提供扩展机制和丰富的生态，能更加灵活地满足需求。

## 2. 部署 LangBot

### 2.1. 部署 LangBot

有三种方式：

1. Docker 部署：[对应链接](https://docs.langbot.app/deploy/langbot/docker.html)
2. 手动部署：[对应链接](https://docs.langbot.app/deploy/langbot/one-click/bt.html)
3. 使用宝塔面板部署：[对应链接](https://docs.langbot.app/deploy/langbot/manual.html)

### 2.2. 对接消息平台

参考[此页面](https://docs.langbot.app/deploy/platforms/readme.html)，选择你要用的消息平台，如 QQ、微信、飞书、钉钉、Telegram、Discord等

## 3. 接入 Dify

在首次运行 LangBot 后，会在 LangBot 目录生成 data 文件夹，打开其中的 `config/provider.json`

设置其中的`runner`为`dify-service-api`

```json
"runner": "dify-service-api",
```

相应的，配置`dify-service-api`

```json
    "dify-service-api": {
        "base-url": "https://api.dify.ai/v1",
        "app-type": "chat",
        "options": {
            "convert-thinking-tips": "original"
        },
        "chat": {
            "api-key": "app-1234567890",
            "timeout": 120
        },
        "agent": {
            "api-key": "app-1234567890",
            "timeout": 120
        },
        "workflow": {
            "api-key": "app-1234567890",
            "output-key": "summary",
            "timeout": 120
        }
    }
```

- `base-url`：Dify Service API 的地址，默认是 `https://api.dify.ai/v1`，这是 Dify 官方云服务的地址，如果你使用的是自部署的社区版，请设置为你的自部署地址。

- `app-type`：使用的 Dify 应用类型。支持 `chat` - 聊天助手（含 Chatflow）、 `agent` - Agent、 `workflow` - 工作流；请填写下方对应的应用类型 API 参数

- `options`：特殊的选项配置。
  - `convert-thinking-tips`：dify 使用 deepseek-r1 等有思维链的模型时[会携带思考过程回复](https://github.com/RockChinQ/LangBot/issues/1108)，此选项控制输出时的处理方式；值为 original 时，不转换思考提示；值为 plain 时，将思考提示转换为类似 DeepSeek 官方的<think>...</think>格式；值为 remove 时，删除思考提示
  
- 
  chat：Dify 聊天助手（或 chatflow）应用的配置
  
  - `api-key`：Dify 聊天助手应用的 API 密钥
  - `timeout`：Dify 聊天助手应用的请求超时时间，以秒为单位，默认是 120 秒。

- 
  agent：Dify Agent 应用的配置
  
  - `api-key`：Dify Agent 应用的 API 密钥
  - `timeout`：Dify Agent 应用的请求超时时间，以秒为单位，默认是 120 秒。

- 
  workflow：Dify 工作流应用的配置
  
  - `api-key`：Dify 工作流应用的 API 密钥
  - `output-key`：Dify 工作流应用的输出键，用于获取工作流应用的输出结果。默认为`summary`，对应工作流编排时，end节点的输出变量。
  - `timeout`：Dify 工作流应用的请求超时时间，以秒为单位，默认是 120 秒。

<figure><img src="../../.gitbook/assets/dify-langbot-provider-workflow-end.png
 " alt=""><figcaption></figcaption></figure>

当使用工作流时，LangBot 会显式传入以下参数，您可以自行在 Dify 工作流的开始节点中添加：

- `user_message_text`：用户消息的纯文本
- `session_id`：用户会话id，私聊为 `person_<id>`，群聊为 `group_<id>`
- `conversation_id`：字符串，用户会话id，由 LangBot 生成。用户重置会话后，会重新生成
- `msg_create_time`：数字类型，收到此消息的时间戳（秒）

您可以[通过插件自定义任何变量](https://docs.langbot.app/plugin/dev/api-ref.html#设置请求变量)

<figure><img src="../../.gitbook/assets/dify-langbot-provider-workflow-start.png
 " alt=""><figcaption></figcaption></figure>

使用 工作流 应用或 Agent 应用时，如果开启了`platform.json`中的`track-function-calls`，将会在 Dify 执行每个工具调用时，输出一个`调用函数xxx`的消息给用户。
但如果是使用`chat`应用下的`ChatFlow`（聊天助手->工作流编排），无论如何只会输出 Answer（直接回复）节点返回的文本。

## 4. 效果展示

> 下面仅为微信和飞书的，其他平台如 QQ、钉钉、Telegram 等平台均可接入

<figure><img src="../../.gitbook/assets/dify-langbot-showcase-wechat.png
 " alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/dify-langbot-showcase-feishu.png
 " alt=""><figcaption></figcaption></figure>