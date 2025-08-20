# 002 - 小白教程-最简单的邮件 AI Agent

[Youtube 教程视频](https://www.youtube.com/watch?v=P9CtkBSOByM&t=550s&ab_channel=%E8%B5%9B%E5%8D%9A%E7%A8%8B%E5%BA%8F%E5%91%98%E8%B5%B5%E5%B8%88%E5%82%85)

## 如何使用此工作流

不需要写代码，不需要编程知识，不需要任何工作流知识。一步一步教你实现一个 N8N Email AI Agent，帮助你出海跟老外用邮件打交道效率高到飞起来。
一步一步的演示，喂饭到你嘴里，让你学会 N8N 这个最实用的 AI 工作流平台的功能。学会这个，你还怕做不出来自己想要的邮件 Agent 效果？
使用自己的 sheet，注意按照工作流指引进行修改！

### 要求

- [OpenRouter API 密钥](https://openrouter.ai/settings/keys)
- [GoogleSheet Oauth2](https://docs.n8n.io/integrations/builtin/credentials/google/oauth-single-service/)
- [Pinecone](https://www.pinecone.io/)

## 额外说明

load contracts 核心流程:

```mermaid
---
config:
      theme: redux
---
sequenceDiagram
    actor alix as Alix
    actor n8n as N8N
    actor doc as GoogleDocs


    alix ->> doc: 添加记录联系人 + Email

    alix ->> n8n: 每天自动触发加载
    n8n ->>+ doc: 请求完整的数据
    doc -->>- n8n: 返回最新的数据
    n8n ->>+ OpenAI: 请求 embedding 模型进行向量化
    OpenAI -->>- n8n: 返回文档的向量
    n8n ->>+ pinecone: 保存向量数据到数据库
    pinecone -->>- n8n: 成功
```

email agent 核心流程:

```mermaid
---
config:
      theme: redux
---
sequenceDiagram
    actor alix as Alix
    actor agent as EmailAgent
    actor llm as OpenAI

    alix->>agent: Hi, 帮我给赵师傅发邮件询问明天有空吗
    agent->>+llm: 规划执行路径
    llm-->>-agent: step1: 通过向量数据库查找赵师傅邮件地址
    agent->>+pinecone: query: 赵师傅
    pinecone-->>-agent: vector: 匹配的数据 top4
    agent->>+llm: 这里是匹配的邮件地址相关信息 top4
    llm-->>agent: step2: 给邮箱 xx@gmail.com 发送消息
    llm-->>agent: step3: 消息主题: "明天有空吗？"
    llm-->>agent: step4: 消息正文: "可以一起吃饭吗？"
    llm-->>-agent: step5: 通过发送邮件工作流发送"
    agent->>+send_email: 发送邮件，参数是 xxx
    send_email-->>agent: 发送成功

    agent-->>alix: 已经为您发送邮件
```
