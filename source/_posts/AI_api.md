---
title: AI Agent Cookbook
categories:
  - AI
tags:
  - AI
date: "2026/06/17 20:46:25"
---

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/%5Blab.magiconch.com%5D%5B%E7%A6%8F%E9%9F%B3%E6%88%B0%E5%A3%AB%E6%A8%99%E9%A1%8C%E7%94%9F%E6%88%90%E5%99%A8%5D-1781699675177.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1781699675177" style="zoom:50%;" />

# 自建 API 站使用说明

本文介绍如何通过自建 API 中转站，统一管理和使用高质量模型 API。主要覆盖三类使用场景：

- 使用 Cherry Studio 进行多模型对话。
- 使用 cc-switch 管理 API 配置与模型代理。
- 在 Codex 或 Claude Code 中接入自建 API 进行编程辅助。

**模型价格表**：http://47.113.198.26/pricing

### 注意

- 包月计费，**一般不限量**。
- 按量计费，**一般按照价格计算**。
- **有些是混合计费，优先使用包月计费，不够用时使用其他渠道补充**。
- **对应的 API KEY 需要按照名字替换**（文档中的 `NEWAPI_CC_KEY` 等为占位符，请替换为从主管处获取的真实 Key）。

## 一、cc-switch：API 配置管理与代理工具

cc-switch 是 API 配置管理和模型代理的核心工具，Claude Code、Codex、Cherry Studio 的配置均依赖它，建议最先安装。

> 提示：请下载最新版本安装，无需与文档截图中的版本一致。

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

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421192539029.png_neo" alt="image-20260421192539029" style="zoom: 67%;" />

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

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260427004611960.png_neo" alt="image-20260427004611960" style="zoom: 67%;" />

```text
供应商名称：NewAPI-cc
API key：NEWAPI_CC_KEY【找主管人员要】
请求地址：http://47.113.198.26
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617212412742.png" alt="image-20260617212412742" style="zoom: 50%;" />

获取模型配置信息：

![image-20260617212444764](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617212444764.png)

#### 使用国产系列模型

推荐使用国产模型配置，高性能且不限量。可先按此处使用 DeepSeek 配置，后续再尝试其他喜欢的模型：

![image-20260617212823420](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617212823420.png)

向下滑动，下侧配置 JSON 应如下所示：

![image-20260617212951811](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617212951811.png)

如果上方看到的不一样，可参考下方内容复制覆盖：

```json
{
  "effortLevel": "xhigh",
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "NEWAPI_CC_KEY【找主管人员要】",
    "ANTHROPIC_BASE_URL": "http://47.113.198.26",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL_NAME": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro[1M]",
    "ANTHROPIC_DEFAULT_OPUS_MODEL_NAME": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro[1M]",
    "ANTHROPIC_DEFAULT_SONNET_MODEL_NAME": "deepseek-v4-pro",
    "CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1,
    "CLAUDE_CODE_EFFORT_LEVEL": "max",
    "ENABLE_TOOL_SEARCH": "true",
    "ANTHROPIC_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_FABLE_MODEL": "deepseek-v4-pro[1M]",
    "ANTHROPIC_DEFAULT_FABLE_MODEL_NAME": "deepseek-v4-pro"
  },
  "includeCoAuthoredBy": false
}
```

注意：API Key 需在一行内填写，不要换行。配置后格式应如下图，如果格式不正确，请点击**格式化**。

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617213025954.png" alt="image-20260617213025954" style="zoom:67%;" />

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
API key：NEWAPI_CC_KEY【找主管人员要】
请求地址：http://47.113.198.26
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617215053339.png" alt="image-20260617215053339" style="zoom:50%;" />

模型配置推荐如下，可参考截图设置：

![image-20260617215256740](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260617215256740.png)

## 四、Codex 接入配置

### 1. 安装 Codex

> 提示：请安装最新版本的 Codex，无需与文档截图中的版本一致。

下载地址：

https://openai.com/zh-Hans-CN/codex/

安装 Codex 后，先关闭 Codex：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222733327.png_neo" alt="image-20260421222733327" style="zoom:67%;" />

### 2. 在 cc-switch 中添加配置

```text
供应商名称：NewAPI codex 专用
Api key：NEWAPI_CODEX_KEY
API 地址：http://47.113.198.26/v1
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421231440252.png_neo" alt="image-20260421231440252" style="zoom:80%;" />

修改完成后，重启 Codex。其他配置保持默认即可正常使用。

Codex 目前仅支持 GPT 系列模型。

## 五、Cherry Studio 综合对话配置

### 1. 添加 API 供应商

打开 Cherry Studio，添加 API 配置：

```text
API 密钥：NEWAPI_CHERRY_KEY
API 地址：http://47.113.198.26
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421231811438.png_neo" alt="image-20260421231811438" style="zoom: 50%;" />

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150012340.png_neo" alt="image-20260506150012340" style="zoom: 33%;" />

### 2. 选择模型并添加到对话

选择需要使用的模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421232309140.png_neo" alt="image-20260421232309140" style="zoom: 67%;" />

在右侧添加模型后，即可在对话中使用。

需要注意的是，不同厂商的模型在添加之后需要修改请求格式。大部分模型不需要修改，部分模型需要修改：

- GPT 系列模型：OpenAI-Response 端点
- Claude 系列模型：Anthropic 端点
- Gemini 系列模型：Gemini 端点
- Kimi 系列模型：OpenAI 端点
- GLM 系列模型：Anthropic 端点 / OpenAI 端点
- MiniMax 系列模型：Anthropic 端点
- Mimo 系列模型：Anthropic 端点

> 修改的原因在于各家接口标准不一致，为了满血体验最好按需修改。

下面举两个例子。添加模型后点击模型设置：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150339942.png_neo" alt="image-20260506150339942" style="zoom: 50%;" />

Claude 系列模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150442656.png_neo" alt="image-20260506150442656" style="zoom:67%;" />

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150459853.png_neo" alt="image-20260506150459853" style="zoom:67%;" />

Google 系列端口修改：

![image-20260507163336410](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260507163336410.png_neo)

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260507163346370.png_neo" alt="image-20260507163346370" style="zoom:67%;" />

其他不需要改动。
