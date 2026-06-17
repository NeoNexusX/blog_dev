---
title: AI Agent Cookbook
categories:
  - AI
tags:
  - AI
date: "2026/06/17 20:46:25"
---

# 自建 API 站使用说明

本文介绍如何通过自建 API 中转站，统一管理和使用高质量模型 API。主要覆盖三类使用场景：

- 使用 Cherry Studio 进行多模型对话。
- 使用 cc-switch 管理 API 配置与模型代理。
- 在 Codex 或 Claude Code 中接入自建 API 进行编程辅助。

**模型价格表**：http://47.113.198.26/pricing

### 注意

- 包月计费，**一般不限量**

- 按量计费，**一般按照价格计算**

- **有些是混合的，优先使用包月计费，不够用了使用其他渠道补充**
- **对应的API KEY需要按照名字替换**

## 一、需要安装的软件

使用前建议先准备以下软件。

### 1. Cherry Studio：多模型聚合对话工具

官网地址：

https://www.cherry-ai.com/

### 2. cc-switch：API 配置管理与代理工具

下载地址：

https://github.com/farion1231/cc-switch/releases/tag/v3.13.0

macOS 选择：

![image-20260421193127674](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421193127674.png_neo)

Windows 选择：

![image-20260421193151129](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421193151129.png_neo)

### 3. 编程助手：Codex 或 Claude Code 二选一

Codex 下载地址：

https://openai.com/zh-Hans-CN/codex/

Claude Code 产品页：

https://claude.com/product/claude-code

Claude Code 可以通过命令行安装，也可以在 **VS Code / PyCharm 插件市场中安装插件**。推荐使用插件方式，下面以 Claude Code 的 VS Code 插件为例说明配置流程。

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421192539029.png_neo" alt="image-20260421192539029" style="zoom: 67%;" />

安装完成后不需要登录，保持当前状态即可：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222815328.png_neo" alt="image-20260421222815328" style="zoom:67%;" />

## 二、Claude Code 接入配置

Claude Code 有两种接入方式：

- **方式一：Claude Code 原生接入**。支持Claude 格式才可以接入，Openai系列模型不支持，通过switch接入支持Claude
- **方式二：通过 cc-switch 的代理接入兼容 OpenAI 的模型**。此方式支持claude系列模型的同时也支持openai系列。

两种方式二选一即可。

### 方式一：Claude Code 原生接入

原生接入支持的模型目前包括：

- claude code 系列模型
- deepseek V4系列模型
- 国产系列模型：GLM5.1等

#### 1. 在 cc-switch 中添加供应商

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222939862.png_neo" alt="image-20260421222939862" style="zoom:67%;" />

向下拉填写下信息：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260427004611960.png_neo" alt="image-20260427004611960" style="zoom: 67%;" />

```bash
供应商名称：NewAPI-cc
API key：NEWAPI_CC_KEY
请求地址：http://47.113.198.26
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260427004921519.png_neo" alt="image-20260427004921519" style="zoom:80%;" />

获取模型配置信息：

![image-20260427004952344](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260427004952344.png_neo)

#### 使用国产系列模型

推荐模型配置：国产模型配置，高性能不限量，主模型空着即可:

![image-20260503123350728](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260503123350728.png_neo)

根据deep seek文档：添加**CLAUDE_CODE_EFFORT_LEVEL** 打开最大强度思考。配置完成后，高级配置部分参考如下，如果和下列不一致【顺序不一样无所谓，效果一样的】请复制后覆盖对应配置：

```bash
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "NEWAPI_CC_KEY",
    "ANTHROPIC_BASE_URL": "http://47.113.198.26",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro",
    "ENABLE_TOOL_SEARCH": "true",
    "CLAUDE_CODE_EFFORT_LEVEL": "max"
  },
  "includeCoAuthoredBy": false
}
```

注意apikey这里没有换行。配置后格式应该如下，如果格式不正确，请点击**格式化**

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260508151621010.png_neo" alt="image-20260508151621010" style="zoom: 50%;" />

#### 使用Claude系列模型

![image-20260505234040012](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260505234040012.png_neo)

**Claude 系列模型只能配合Claude 系列模型，不能和国产混用**：

下面的配置需要添加到配置文件里面

```
"CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS": "1"
```

添加到配置JSON下面即可，添加完成之后整体效果如下：

```bash
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "NEWAPI_CC_KEY",
    "ANTHROPIC_BASE_URL": "http://47.113.198.26",
    "ANTHROPIC_MODEL": "claude-opus-4-7",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "claude-haiku-4-5",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "claude-sonnet-4-6",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "claude-opus-4-7",
    "CLAUDE_CODE_EFFORT_LEVEL": "max",
    "ENABLE_TOOL_SEARCH": "true",
    "CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS": "1"
  }
}
```

如果和上述不一致【顺序不一样无所谓，效果一样的】请复制后覆盖对应配置。

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260508151655930.png_neo" alt="image-20260508151655930" style="zoom:50%;" />

#### 3. 重启 VS Code 并开始使用

配置完成后，重新打开 VS Code。看到对应状态后，即可开始使用 Claude Code 进行编码：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421224052290.png_neo" alt="image-20260421224052290" style="zoom:80%;" />

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421224101637.png_neo" alt="image-20260421224101637" style="zoom: 33%;" />

## 三、Codex 接入配置

安装 Codex 后，先关闭 Codex：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421222733327.png_neo" alt="image-20260421222733327" style="zoom:67%;" />

然后在 cc-switch 中添加配置：

```bash
供应商名称：NewAPI codex 专用
Api key：NEWAPI_CODEX_KEY
API 地址：http://47.113.198.26/v1
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421231440252.png_neo" alt="image-20260421231440252" style="zoom:80%;" />

修改完成后，重启 Codex。其他配置保持默认即可正常使用。

## 四、Cherry Studio 综合对话配置

### 1. 添加 API 供应商

打开 Cherry Studio，添加 API 配置：

```bash
API 密钥：NEWAPI_CHERRY_KEY
API 地址：http://47.113.198.26
```

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421231811438.png_neo" alt="image-20260421231811438" style="zoom: 50%;" />

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150012340.png_neo" alt="image-20260506150012340" style="zoom: 33%;" />

### 2. 选择模型并添加到对话

选择需要使用的模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260421232309140.png_neo" alt="image-20260421232309140" style="zoom: 67%;" />

在右侧添加模型后，即可在对话中使用。

需要注意的是，不同厂商的模型，再选择添加之后需要修改请求格式：

大部分模型都不需要修改，部分模型需要修改：

- GPT-5.5
- GPT-5.4
- Claude系列模型
- Gemini系列模型
- **修改的原因在于各家链接标准不一致，为了满血体验最好修改**

添加模型后点击模型设置：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150339942.png_neo" alt="image-20260506150339942" style="zoom: 50%;" />

上述模型需要选择：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150407203.png_neo" alt="image-20260506150407203" style="zoom:67%;" />

Claude系列模型：

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150442656.png_neo" alt="image-20260506150442656" style="zoom:67%;" />

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260506150459853.png_neo" alt="image-20260506150459853" style="zoom:67%;" />

Google系列端口修改：

![image-20260507163336410](https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260507163336410.png_neo)

<img src="https://neonexus-picture.oss-ap-southeast-1.aliyuncs.com/test/image-20260507163346370.png_neo" alt="image-20260507163346370" style="zoom:67%;" />

其他不需要改动。
