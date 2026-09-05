---
title: AI Agent Cookbook
categories:
  - AI API
tags:
  - AI API
date: "2026/09/01 09:46:25"
---

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/%5Blab.magiconch.com%5D%5B%E7%A6%8F%E9%9F%B3%E6%88%B0%E5%A3%AB%E6%A8%99%E9%A1%8C%E7%94%9F%E6%88%90%E5%99%A8%5D-1781699675177.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1781699675177" style="zoom:50%;" />

# 自建 API 站使用说明

本文介绍如何通过自建 API 中转站，统一管理和使用高质量模型 API。主要覆盖三类使用场景：

- 使用 Cherry Studio 进行多模型对话。
- 使用 cc-switch 管理 API 配置与模型代理。
- 在 Codex 或 Claude Code 中接入自建 API 进行编程辅助。

**模型表**：http://47.113.198.26/pricing

### 注意

- **文档中的 `NEWAPI_CC_KEY`、`NEWAPI_CODEX_KEY`、`NEWAPI_CHERRY_KEY` 等均为占位符，请统一替换为你在第零节注册账号后自行创建的 API Key。**

## 零、注册账号并获取 API Key

访问以下地址注册账号：

https://api.neonexus.top/

<img src="https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/image-20260811114613406.png_view" alt="image-20260811114613406" style="zoom:50%;" />

创建 API Key：

![image-20260811114709642](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/image-20260811114709642.png_view)

创建令牌时，建议根据实际使用场景选择对应的分组：

- Claude Code：请选择 Claude Code 专用分组。
- Cherry Studio：支持多种接口，可选择 Auto 分组，由系统自动匹配。
- Codex：仅支持 Responses 接口，因此使用 Codex 时请选择 Codex 专用分组。

<img src="https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811131858643.jpg_view" alt="image-20260811131858580" style="zoom: 33%;" />

默认令牌分组选择 `auto` 即可，但是模型显示可能不准确，有些模型仅支持claude接口，有些仅openai接口，如果想先试试，可以先用auto，如果选了一直没有回复或者报错，请把使用记录发给管理人员：

<img src="https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811115510626.jpg_view" alt="image-20260811115510027" style="zoom: 50%;" />

下文各章节需要填写的 API Key，均在这里自行创建并复制：

![image-20260811115819225](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811115819301.png_view)



## 一、cc-switch：API 配置管理与代理工具

cc-switch 是 API 配置管理和模型代理的核心工具，Claude Code、Codex、Cherry Studio 的配置均依赖它，建议最先安装。

> 提示：**请下载最新版本安装，无需与文档截图中的版本一致。**

下载地址：

https://github.com/farion1231/cc-switch/releases

macOS 选择：

![image-20260421193127674](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421193127674.png_neo)

Windows 选择：

![image-20260421193151129](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421193151129.png_neo)

## 二、Claude Code 插件和命令行版本接入

### 安装 Claude Code

Claude Code 可以通过命令行安装，也可以在 **VS Code / PyCharm 插件市场**中安装插件，推荐使用插件方式。

> 提示：请安装最新版本的 Claude Code，无需与文档截图中的版本一致。

VS Code 插件安装：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421192539029.png_neo" alt="image-20260421192539029" style="zoom: 50%;" />

安装完成后不需要登录，保持当前状态即可：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222815328.png_neo" alt="image-20260421222815328" style="zoom:67%;" />

**桌面版与插件可以分开使用，二者在 cc-switch 中是独立渠道，无需来回切换配置即可切换模型。例如可以把桌面版设置为一个高速模型来回答问题，插件侧用于编码。桌面版的独立配置详见第三节**：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617211940772.png" alt="image-20260617211940772" style="zoom: 33%;" />

### 插件和命令行版本接入

接入支持的模型目前包括：

- DeepSeek V4 系列模型
- GLM 系列
- Kimi 系列
- MiniMax 系列 

#### 1. 在 cc-switch 中添加供应商

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222939862.png_neo" alt="image-20260421222939862" style="zoom:67%;" />

向下滚动，填写以下信息：

```text
供应商名称：NewAPI-cc
API key：NEWAPI_CC_KEY【替换为你在第零节自行创建的 API Key】
请求地址：http://47.113.198.26 【注意地址不要填写域名，请填写ip】
```

<img src="https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811123731828.jpg_view" alt="image-20260811123731770" style="zoom:50%;" />

获取模型配置信息：

![image-20260617212444764](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617212444764.png)

#### 使用国产系列模型

推荐使用国产模型配置，高性能且不限量。可先按此处使用配置，后续再尝试其他喜欢的模型：

<img src="https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811122426168.png_view" alt="image-20260811122426075" style="zoom:67%;" />

向下滑动，下侧配置 JSON 应如下所示：

<img src="https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811124748894.jpg_view" alt="image-20260811124748837" style="zoom:67%;" />

如果上方看到的不一样，可参考下方内容复制覆盖：

```json
{
  "effortLevel": "max",
  "enableWorkflows": true,
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "你的 API Key",
    "ANTHROPIC_BASE_URL": "http://47.113.198.26",
    "ANTHROPIC_DEFAULT_FABLE_MODEL": "k3[1M]",
    "ANTHROPIC_DEFAULT_FABLE_MODEL_NAME": "k3",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL_NAME": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "k3-256k",
    "ANTHROPIC_DEFAULT_OPUS_MODEL_NAME": "k3-256k",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-5.2[1M]",
    "ANTHROPIC_DEFAULT_SONNET_MODEL_NAME": "glm-5.2",
    "ANTHROPIC_MODEL": "k3[1M]",
    "CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1,
    "CLAUDE_CODE_EFFORT_LEVEL": "max",
    "CLAUDE_CODE_SUBAGENT_MODEL": "deepseek-v4-pro[1M]",
    "ENABLE_TOOL_SEARCH": "true"
  },
  "includeCoAuthoredBy": false,
  "model": "haiku",
  "permissions": {
    "allow": [
      "Bash(curl -fsSL *)"
    ]
  }
}
```

注意：API Key 需在一行内填写，不要换行。配置后格式应如下图，如果格式不正确，请点击**格式化**。

![image-20260811123000735](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811123251292.png_view)

#### 2. 重启 VS Code 并开始使用

配置完成后，重新打开 VS Code。看到对应状态后，即可开始使用 Claude Code 进行编码：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421224052290.png_neo" alt="image-20260421224052290" style="zoom:80%;" />

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421224101637.png_neo" alt="image-20260421224101637" style="zoom: 33%;" />

## 三、Claude 桌面版接入

### 1. 桌面版下载

Claude Code 桌面版下载页：

https://claude.com/product/claude-code

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617211734767.png" alt="image-20260617211734767" style="zoom: 33%;" />

### 2. CC Switch 添加供应商

```text
供应商名称：NewAPI-cc
API key：NEWAPI_CC_KEY【替换为你在第零节自行创建的 API Key】
请求地址：http://47.113.198.26
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617215053339.png" alt="image-20260617215053339" style="zoom:50%;" />

## 四、Codex 接入配置

### 1. 安装 Codex

> 提示：**请安装最新版本的 Codex，无需与文档截图中的版本一致。**

下载地址：

https://openai.com/zh-Hans-CN/codex/

安装 Codex 后，先关闭 Codex：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222733327.png_neo" alt="image-20260421222733327" style="zoom:67%;" />

### 2. 在 cc-switch 中添加配置

![image-20260715122432874](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260715122432874.png)

![image-20260715122513649](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260715122513649.png)

```text
供应商名称：NewAPI codex 专用
API key：NEWAPI_CODEX_KEY【替换为你在第零节自行创建的 API Key】
API 地址：http://47.113.198.26/v1
```

![image-20260715140942010](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260715140942010.png)

只需要填写 API 地址和你在第零节自行创建的 API Key，其他配置保持默认即可。保存配置后请重启 Codex。

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260715122737312.png" alt="image-20260715122737312" style="zoom:50%;" />

### Codex 国产模型接入

Codex 国产模型接入，目前已经更新支持国产模型，优先推荐使用GPT原生模型，不够用了再换国产，目前支持的：

国产模型包括：

- **GLM-5.2**
- **K3**，**kimi-k2.7-code**
- **DeepSeek V4 Flash**
- **MiMo**
- **muse-spark-1.2-contributor**

在CCswitch 里面：选择对应的模型，然后保存，**重启codex**，**记住一定要重启**：

![image-20260715141218167](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260715141218167.png)

![image-20260811130247478](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811130247524.jpg_view)

登录官网使用记录可以看到对应的模型信息：

![image-20260811130339073](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811130339648.jpg_view)

## 五、Cherry Studio 综合对话配置

### 1. 添加 API 供应商

打开 Cherry Studio，添加 API 配置：

```text
API 密钥：NEWAPI_CHERRY_KEY【替换为你在第零节自行创建的 API Key】
API 地址：http://47.113.198.26
```

搜索New API：

![image-20260903153108473](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903153108514.jpg/kawu_tech_ez)

![image-20260903152714971](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903152715067.jpg/kawu_tech_ez)

填写上述信息。

### 2. 选择模型并添加到对话

选择需要使用的模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421232309140.png_neo" alt="image-20260421232309140" style="zoom: 67%;" />

在右侧添加模型后，即可在对话中使用。

需要注意的是，不同厂商的模型在添加之后需要修改请求格式。大部分模型不需要修改，部分模型需要修改：

- **GPT 系列模型：OpenAI-Response 端点**
- **Claude 系列模型：Anthropic 端点**
- **Gemini 系列模型：Gemini 端点**；**特殊：Gemini-3.7 flash ：OpenAI 端点**
- **Kimi 系列模型：OpenAI 端点**
- **GLM 系列模型：Anthropic 端点 / OpenAI 端点**
- **MiniMax 系列模型：OpenAI 端点**
- **MiMo 系列模型：Anthropic 端点**

> 修改的原因在于：各家接口标准不一致，中转战虽然会自动转换，但是为了满血体验，避免降智最好按需修改。

下面举两个例子。添加模型后点击模型设置：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903135402049.jpg_neo" alt="image-20260903135354934" style="zoom: 50%;" />

更新模型端点：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903135517154.jpg_neo" alt="image-20260903135517096" style="zoom:67%;" />

需要注意的是，请不要使用下列模型，下列模型是为了上下文探测使用的，请不要主动调用：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903135902725.jpg_neo" alt="image-20260903135902694" style="zoom:50%;" />

Google 系列端口修改：

![image-20260507163336410](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260507163336410.png_neo)

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260507163346370.png_neo" alt="image-20260507163346370" style="zoom: 50%;" />

### 3.选择生图模型并添加到对话

新增生图模型，GPT-image-2，设置如下：

首先添加生图模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903141233442.jpg_neo" alt="image-20260903141233401" style="zoom: 50%;" />

然后配置模型链接信息：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903142016754.jpg_neo" alt="image-20260903142016699" style="zoom:50%;" />

填写：`http://47.113.198.26/v1`

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903143302092.jpg/kawu_tech_ez" alt="image-20260903143302045" style="zoom: 50%;" />

同时设置默认模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903145113066.jpg/kawu_tech_ez" alt="image-20260903145113000" style="zoom: 50%;" />

在使用的时候工具栏要开启生成：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903150435441.jpg/kawu_tech_ez" alt="image-20260903150435395" style="zoom:50%;" />

效果：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/20260903150243829.jpg/kawu_tech_ez" alt="image-20260903150243761" style="zoom:50%;" />



## QA：常见报错及其原因

#### 1.没有回复，回复比较慢，status_code=500，400等问题

**status_code=500, not implemented**

**status_code=400, Error from provider (Console): Upstream request failed: [invalid_request_error] Failed to deserialize the JSON body into the target type: tools[14].function: missing field `name` at line 1 column 17015**

api 密钥的端点选择不正确，请去后台查看使用记录，应该选择软件适配对应的端点：

![image-20260811145637249](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811145637353.jpg_view)

生成应用对应的令牌即可继续使用：

![image-20260811145948939](https://official-oss.oss-cn-hongkong.aliyuncs.com/docs/20260811145949049.jpg_view)

#### 2.Cherry studio报错 端点类型 不正确

```bash
codex channel: /v1/chat/completions endpoint not supported (request id: 202607150616095162701178268d9d6OnzYLdSu)
```

这里的报错正好相反，这里是cherry studio软件的端口选择不正确，根据上文选择对应的端点类型即可解决：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260715141753537.png" alt="image-20260715141753537" style="zoom: 50%;" />

请根据上边内容调整端点类型，Chatgpt系列模型默认是 **Response 模式**。

#### 3.报错 502 upstream error

**近期梯子不太稳定，如果频繁出现，请联系管理员切换备用梯子，或者先使用国产模型。**
