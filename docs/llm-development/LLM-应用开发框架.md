---
title: LLM 应用开发框架
date: 2024-10-29 10:00:00
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku97.jpeg
tags:
  - LLM
  - AI
categories:
  - LLM
---

## 前言

自从 GPT-4 和 ChatGPT 问世以来，大语言模型的热潮仿佛到来，各种大模型和 AIGC 的产品层出不穷。LLM
开发框架可以说也是大语言模型热潮下的一个产物和趋势。

<!-- more -->

什么是 LLM 开发框架呢？简单来说就是提供大语言模型应用业务的开发基础和快速使用的能力。因为单纯的大语言模型是比较难去实现一些复杂的场景和业务需求的。比方说
ChatBot 的使用场景（即聊天对话机器人，可以类比于 ChatGPT），实际上大语言模型只会根据你的「输入」来预测你接下来的「输出」，它并没有存储你之前对话的能力，这个时候可能就需要一个
”Memory“ 组件来提供这样一个存储上下文的能力。再比方说一个文档问答机器人，它是如何根据你的问题去搜索对应的文档或者结果的呢？比较简单的方法其实是去结合向量数据库来做，通过将文档切片、向量化、也可能做一些对于文档的
summary 来存储到向量数据库中（比较知名的向量数据库比如 Milvus，Elasticsearch
也可以作为向量数据库使用）。大语言模型通过将用户的问题转化为向量，到向量数据库中进行相似度搜索和召回，这样就可以得到一系列和你问题相关的文档。但是
LLM 本身其实是没有这个能力的，这也是 LLM 开发框架需要去做和集成的一些组件和功能。

目前最有名的 LLM 开发框架非 LangChain 莫属，Java 版的有 Langchain-java、Langchain4j 等一系列开源库。

## LLM 开发框架具体做的事

LLM framework 主要提供组件集成与封装、调用链路追踪（LangSmith）、Agent 开发（LangGraph）等功能，能够帮助开发者快速上手 LLM
应用的开发，使开发者免去繁琐的各种第三方应用的组件集成时间。比如：我想要使用 OpenAI 的模型和 Milvus 作为向量数据库来做
RAG，我不需要再去一步步实现文档切割、 Milvus 的数据插入和数据搜索，再合成 Prompt 输入到 OpenAI 模型中。通过 LangChain 等 LLM
framework，开发者能够快速的构建起相关应用，将精力放在业务逻辑上。

除此之外，**链路追踪**也是 LLM 应用中需要关注的事，由于 LLM 应用中涉及复杂的组件交互，模型请求和响应又比较容易超时和返回非预期的响应。因此，LLM
应用的链路追踪也是极其重要的一部分。[LangSmith](https://docs.smith.langchain.com/)
就是为了这个而诞生（当然不止有这个特性），其内部提供了对于链路追踪的可观测性，以及对于模型结果开销的评估，同时也提供了对于提示工程（Prompt
Engineering）的管理和快速使用。通过 LangSmith，LLM 应用可以快速的进行调试和监控，从而保证应用的稳定性和可观测性。

如今的 LLM 应用大多是以智能体（Agent）的形式进行构建和开发。Agent 指的是通过 LLM 解决复杂问题的能力，LLM
在其中需要通过思考、推理和工具调用从而解决一系列复杂的问题，如今的智能体分为单智能体和多智能体两种类别，多智能体框架是目前出现较多的框架，比如 [AutoGPT](https://github.com/Significant-Gravitas/AutoGPT)
和 [MetaGPT](https://github.com/geekan/MetaGPT)。

## 组件类别

下面具体介绍一下 LLM 应用开发框架中主要封装的组件类别。

### Model

最重要的其实是 Chat Model，也就是在聊天消息界面通过消息列表作为输入，并返回消息进行输出。最新的聊天模型通常提供了下面三个能力：

1. Tool calling：工具调用，许多流行的聊天模型都提供原生工具调用 API。此 API 允许开发人员构建丰富的应用程序，使 AI 能够与外部服务、API 和数据库进行交互。工具调用还可用于从非结构化数据中提取结构化信息并执行各种其他任务。
2. Structured Output：结构化输出，一种使聊天模型以结构化格式（例如与给定架构匹配的 JSON）响应的技术。
3. Multimodality：多模态，能够处理文本以外的数据;例如，图像、音频和视频。

### Embedding Store

向量数据库

### Memory Store

聊天记忆的存储

### Document Loader

文档加载器

### Toolkits

各种工具

## Reference

1. [LangChain Document](https://python.langchain.com/docs/introduction/)
2. [LangSmith Document](https://docs.smith.langchain.com/)
3. [LangGraph Document](https://langchain-ai.github.io/langgraph/)
4. [LangChain4j Document](https://docs.langchain4j.dev/)
