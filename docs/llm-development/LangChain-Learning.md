---
title: LangChain Learning
date: 2023-08-12 19:37:51
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku96.jpeg
tags:
   - LLM
   - AI
categories:
   - LLM
---

## What is LangChain

[LangChain](https://github.com/langchain-ai/langchain) 是一个使用 Python 开发的，**用于开发由语言模型（language models）驱动的应用程序的框架**。LangChain 的开发旨在让开发人员不单单是通过 API 来使用 LLM 模型，而且还可以：

1. 具有数据感知能力：将语言模型连接到其他数据源
2. 具有主体性：允许语言模型与环境交互

因此，LangChain 框架的设计目标就是支持这些类型的应用程序。

<!-- more -->

> NOTE: 可以理解为 LangChain 其实就是基于语言模型 API 提供封装和更多特性，让开发者可以不用直接去和 API 调用打交道，而是通过封装好的方法调用来和语言模型交互，并且可以通过新特性（比如其他数据源来增强语言模型的场景能力）来增强语言模型的能力

在 LangChain 中主要提供了两种属性：

1. Components：LangChain 为使用语言模型必要的组件做了**模块化抽象（modular abstractions）**。LangChain 也有一系列关于这些模块化抽象的实现，这些 componenet 被设计为易用的，无论你是否使用 LangChain 框架的其他部分。
2. Use-Case Specific Chains：Chains（可以翻译为调用链）可以被认为是以特定的方式组装这些组件，以便最好地完成特定的用例。这些旨在成为一个更高层次的接口，通过它人们可以轻松地开始使用特定的用例。这些 Chains 也是可定制的。

## Components

在 LangChain 中一共有六种 component，包括 Schema、Models、Prompts、Indexes、Memory、Chains、Agents。下面会分别介绍这六个 component。

### Schema

Schema（我觉得可以翻译为类似数据结构的意思） 中主要包括 Text、ChatMessages、Examples、Document 四种类型。

#### Text

当使用语言模型时，可以通过 Text（文本）作为与它们交互的主要接口。由于过度简化，许多模型都是"text in, text out"。因此，LangChain 中的许多接口都以文本为中心。

#### ChatMessages

终端用户与语言模型进行交互的主要接口是通过 ChatMessages。出于这个原因，一些模型提供者甚至开始**期望以 ChatMessages 的方式提供对底层 API 的访问**。这些消息有一个内容字段（通常是文本），并与用户关联。目前支持的用户是 System、Humane 和 AI：

1. SystemChatMessage：代表信息的聊天消息，这些信息应该是AI系统的指令。
2. HumanChatMessage：表示来自人类与 AI 系统交互的信息的聊天消息。
3. AIChatMessage：表示来自 AI 系统的信息的聊天消息

#### Examples

Examples 是代表对函数的输入和预期输出的一个输入/输出对（即通过给定一个示例的输入和输出来告知语言模型），通常 Examples 可以用来**训练和评估模型**。Examples 可以是模型或 Chains 的输入/输出。这两种 Examples 有着不同的目的：

1. 对模型的 Examples 可以看做是**对模型的调优**（通常会通过给定语言模型一个例子来让语言模型调整和优化回答方向和模式）。
2. 对 Chains 的 Examples 用于**评估端到端链（end-to-end chain）**，甚至可以训练一个模型来替换整个链。

#### Document

Document 通常由一系列无结构的数据组成，可以看做由 `page_content`（这些文档数据的内容） 和 `metadata`（描述数据属性的辅助信息） 组成。

### Models

在 LangChain 中可以使用不同的模型作为底层提供，目前主要包括 LLM、Chat Models、Text Embedding Models 三个类别。

#### LLM -- 大语言模型

通常以一个 Text（文本）为输入，然后以另一个 Text（文本）为输出。可以参考 GPT 的模式

#### Chat Models -- 聊天模型

通常由语言模型支持，但是它的 API 会更加结构化。通常这些模型会由一系列的 ChatMessages 组成输入，然后以一条 ChatMessage 作为输出。（一般会将上下文的聊天消息以某种方式持久化下来，比如通过内存数据库 H2 的方式，然后在用户输入一条 ChatMessage 的时候，将内存中的上下文聊天消息都取出作为模型的输入）。

#### Text Embedding Models -- 文本嵌入模型

以 Text 为输入，然后返回一个浮点数列表（对文本做编码形成向量，个人理解类似为 NLP 中的 word embedding 和 sentence embedding）。

### Prompts

> 1. 关于 prompt engineering 可以参考 [ChatGPT Prompt Engineering for Developers](https://www.bilibili.com/video/BV1514y1Z7z8/)
> 2. 一些比较有用的 prompt 可以参考 [awesome-chatgpt-prompts](https://github.com/f/awesome-chatgpt-prompts)

对模型进行编程（其实就是对模型进行更细化的调整和暗示，以使模型能够更符合某个场景下的文档）的新方法是通过 prompts。“提示”指的是模型的输入。这种输入很少硬编码，而是经常由多个组件构成。`PromptTemplate` 负责构造这个输入。LangChain 提供了一些类和函数来简化构造和使用提示符。

#### PromptValue

`PromptValue` 代表着对模型的输入。在 LangChain 中针对提示的主要抽象都是为了处理文本数据，对于其他数据类型（图像、音频等）还不支持。使用 `PromptValue` 的主要目的是为了**将一个提示使用在不同的模型上**。它公开了可以转换为每个模型类型期望的确切输入类型的方法（目前是 Text 或 ChatMessages 类型）。

#### Prompt Templates

`Prompt Template` 负责构造一个 `PromptValue`。大多数场景下都会通过用户输入的组合、其他非静态的信息（通常来自于多个数据源）和模板字符串来动态创建一个 `PromptValue`，负责创建的对象被叫做 `PromptTemplate`。它公开了可以将输入的变量转化为 `PromptValue` 的方法。

#### Example Selectors

`Example Selectors` 在 prompt 中包含示例通常是有用的。这些示例可以硬编码，但动态选择它们通常更强大。`Example Selectors` 是一个读取用户输入然后返回一个可以使用的示例列表的对象。

#### Output Parsers

语言模型（和聊天模型）输出文本。但很多时候，你可能想要得到更多结构化的信息，而不仅仅是单纯的文本回答。这就是 `Output Parser`（输出解析器）的作用。`Output Parser` 负责：

1. 指示模型应如何格式化输出。
2. 将输出解析为所需的格式（包括在必要时重试）。

在 Langchain 中，主要有两个 `Output Parser` 必须要要实现的方法：

1. `get_format_instructions() -> str`：该方法返回一个字符串，其中包含有关如何格式化语言模型输出的指令。
2. `parse(str) -> Any`：该方法接受一个字符串（假设是来自语言模型的响应），然后将其转化为某个结构

以及一个可选的方法：

1. `parse_with_prompt(str) -> Any`：该方法接受一个字符串（假设是来自语言模型的响应）和一个 prompt（假设是生成这种响应的提示符），并将其解析为某种结构。该提示主要是在 `OutputParser` 希望以某种方式重试或修复输出，并需要提示中的信息来执行此操作的情况下提供的。

### Indexes

索引指的是组织文档的方法，以便 LLM 可以更好的与它们交互。在链中使用索引最常见的方式是在“检索”（retrieval）步骤中。这一步指的是获取用户的查询并返回最相关的文档。之所以这样区分，是因为：

1. 索引除了用于检索之外，还可以用于其他事情。
2. 检索可以使用索引之外的其他逻辑来查找相关文档。因此，LangChain 又一个 `Retriever` 接口的概念-这是大多数链工作的接口。

大多数时候，当我们谈论索引和检索时，我们谈论的是索引和检索非结构化数据（如文本文档）。对于与结构化数据（SQL表等）或 API 交互，请参阅相应的用例部分以获取相关功能的链接。目前，LangChain 支持的主要索引和检索类型都是围绕向量数据库展开的。

#### Document Loaders

`DocumentLoaders` 负责加载不同来源的文档.

#### Text Splitters

`TextSplitters` 负责将文本分割为更小的单元。

#### VectorStores

`VectorStores` 是索引中最常见的类型，它依赖于嵌入（embedding）。`VectorStores` 存储文档并且关联潜入，提供一种快速的方法来通过 embedding 查找相关的文档。

#### Retrievers

`Retrievers` 是一个获取相关文档的接口，以与语言模型相结合。该接口只需要暴露 `get_relevant_texts` 方法，该方法将一个字符串作为输入并返回一系列相关的文档。

### Memory

Memory 是指在对话过程中存储和检索数据的概念。主要有两种方法:

1. 根据输入，获取任何相关数据。
2. 根据输入和输出，相应地更新状态。

Memory 有两种主要类型：短期记忆（short term）和长期记忆（long term）。短期记忆通常指的是如何在单个对话的上下文中传递数据（通常是之前的聊天消息或它们的摘要）。长期记忆处理如何在对话之间获取和更新信息。

#### Chat Message History

`ChatMessageHistory` 类负责记住所有之前的聊天交互。然后可以直接将这些参数传递回模型，以某种方式进行总结，或者进行某种组合。它主要暴露两个方法和一个属性，分别是：

1. `add_user_message()`：存储来自用户的消息。
2. `add_ai_message()`：存储来自 AI 的消息。
3. `messages`：用来获取之前的消息。

### Chains

Chains 返回到以特定方式组合的模块化组件序列（或其他链）以完成常见的用例。最常用的链类型是 LLMChain ，它结合了 `PromptTemplate`、模型和 Guardrails 来获取用户输入，然后相应地格式化它，将其传递给模型并获得响应，然后验证和修复（如果需要）模型输出。

#### Chain

Chain 指的是多个独立组件的端到端包装器。

#### LLMChain

LLMChain 是最常见的一种链类型，它由 `PromptTemplate`、一个模型（可能是 LLM 或者 ChatModel）、一个可选的 `OutputParser` 组成。该链将多个变量作为输入，使用 `PromptTemplate` 来将其格式化为一个 prompt，然后将该 prompt 传递给模型。最后，它使用 `OutputParser`（如果有）来格式化从模型得到的输出。

#### Index-related chains

这类链用于与索引（Index）交互。这些链的目的是将存储在索引中的数据与 LLM 相结合。最好的例子是在你自己的文档中回答问题（即在文档中检索答案并回答问题）。

其中很大一部分是理解如何将多个文档传递给语言模型。有几种不同的方法（或链）可以做到这一点。LangChain 支持四种常见的方式来传递，分别是：Stuffing、Map Reduce、Refine、Map-Rerank

1. Stuffing：Stuffing 是最简单的方法，只需将所有相关数据作为上下文填充到 prompt 中，以传递给语言模型。这在 LangChain 中实现为 `StuffDocumentsChain`。
   1. 优点：只会调用一次 LLM。当生成文本时，LLM可以一次访问所有数据。
   2. 缺点:大多数 LLM 都有上下文长度的限制，对于大型文档（或数量比较多的文档），这将不起作用，因为它将导致提示大于上下文长度。

   这种方法的主要缺点是它只能处理较小的数据块。一旦需要处理大量数据，这种方法就不再可行。下面两种方法就是为了解决这个问题而设计的。
2. Map Reduce：该方法涉及在每个数据块上运行一个初始提示（对于汇总任务，这可能是该数据块的摘要；对于问答任务，它可以是一个完全基于该块的答案）。然后运行一个不同的 prompt 来合并所有的初始输出。这在 LangChain 中以 `MapReduceDocumentsChain` 的形式实现。
   1. 优点：可以扩展到比 `StuffDocumentsChain` 更大的文档（和更多的文档）。对单个文档的 LLM 调用是独立的，因此可以并行化。
   2. 缺点：需要比 `StuffDocumentsChain` 更多的LLM调用。在最后的联合调用期间丢失一些信息。
3. Refine：这个方法涉及在第一块数据上运行一个初始提示符，生成一些输出。对于剩余的文档，该输出连同下一个文档一起传入，要求 LLM 根据新文档改进输出。
   1. 优点：可以获取更多相关的上下文，并且可能比 `MapReduceDocumentsChain` 损耗更小。
   2. 缺点：需要比 `StuffDocumentsChain` 更多的 LLM 调用。这些调用也不是独立的，这意味着它们不能像 `MapReduceDocumentsChain` 那样并行。文档的顺序也有一些潜在的依赖关系。
4. Map-Rerank：这种方法涉及在每个数据块上运行一个初始提示，不仅尝试完成任务，还会根据答案的确定程度打分。然后根据得分对响应进行排序，并返回得分最高的答案。
   1. 优点：与 `MapReduceDocumentsChain` 的优点类似。但是与 `MapReduceDocumentsChain` 相比，需要更少的调用。
   2. 缺点：它不能组合文档之间的信息。这意味着，当您希望在单个文档中有一个简单的答案时，它是最有用的。

#### Prompt Selector

LangChain 中链的目标之一是使用户能够尽快开始使用在特定场景下的用例。要满足这个目的，很大一部分程度是需要一个好的 prompt。

问题是，适用于一个模型的 prompt 可能不适用于另一个模型。我们希望使链能够很好地适用于所有类型的模型。因此，我们有 `PromptSelector` 的概念，而不是硬编码在链中使用的默认提示符。这个 `PromptSelector` 负责根据传入的模型选择默认提示。

`PromptSelectors` 最常见的用例是为 LLMs 和 ChatModel 设置不同的默认提示。然而，这也可以用来为不同的模型提供者设置不同的默认提示。

### Agents

一些应用程序不仅需要对 LLMs/其他工具的预定调用链，而且可能需要依赖于用户输入的未知链。在这些类型的链中，有一个 Agent（类似于代理）可以访问一套工具。根据用户的输入，代理可以决定调用哪些工具（如果有的话）。在 Agents 中有以下四个重要概念：

1. Tools：语言模型如何与其他资源交互。Tools 是围绕函数的特定抽象，使语言模型可以轻松地与之交互。具体来说，工具的接口只有一个文本输入和一个文本输出（text in, text out）。
2. Agents：驱动决策的语言模型。可以看做是一个模型的封装，它接受用户输入，并返回与要执行的“操作”（action）和相应的“操作输入”（action input）相对应的响应。

    > 比如用户输入是查询订单，那么响应的响应可能就是一系列订单数据。这里的 action 就是查询订单，action input 可能是两个日期，要查询这两个日期间的订单
3. Toolkits：组合使用可以完成特定任务的工具集。
4. Agent Executor：用工具运行代理的逻辑。代理执行器负责调用代理，获取操作和操作输入，使用相应的输入调用操作引用的工具，获取工具的输出，然后将所有信息传递回代理，以获取它应该采取的下一个操作。

## Use-Case

下面介绍一些 LangChain 可以处理的场景，在每个场景中会讨论哪一个 component 是对解决这个场景最有帮助的

### Personal Assistants

### Question Answering Over Docs

### Chatbots

### Querying Tabular Data

### Interacting with APIs

### Extraction

### Evaluation

### Summarization

## Example

下面以 [langchain4j](https://github.com/langchain4j/langchain4j) 为例子来展示如何使用类 LangChain 框架来完成快速的语言模型应用程序开发。

> langchain4j 是一个 Java 版 LangChain，使用它为例子主要是因为其有提供 Spring Boot Starter 的能力，能够方便我们快速在 Spring Boot 中集成它
>
> All Example are avaliale on [langchain4j-example](https://github.com/langchain4j/langchain4j-examples/blob/main/spring-boot-example/src/test/java/dev/example/CustomerSupportApplicationTest.java)

### What langchain4j do

先来看看 langchian4j 都为我们提供了哪些自动装配的 Bean：

```java
@AutoConfiguration
@EnableConfigurationProperties(LangChain4jProperties.class)
public class LangChain4jAutoConfiguration {

    @Bean
    @Lazy
    @ConditionalOnMissingBean
    ChatLanguageModel chatLanguageModel(LangChain4jProperties properties) {
       // 聊天模型的模型 Bean，这里省去具体的实现细节
       // 基本上就是根据配置来返回对应的聊天模型对象，主要有 OpenAI、HuggingFace 和 Local 三种模型可以选择。
    }

    @Bean
    @Lazy
    @ConditionalOnMissingBean
    LanguageModel languageModel(LangChain4jProperties properties) {
        // LLM 的模型 Bean，这里省去具体的实现细节
        // 基本上就是根据配置来返回对应的 LLM 模型对象，主要有 OpenAI、HuggingFace 和 Local 三种模型可以选择
    }

    @Bean
    @Lazy
    @ConditionalOnMissingBean
    EmbeddingModel embeddingModel(LangChain4jProperties properties) {
        // 嵌入模型的模型 Bean，这里省去具体的实现细节
        // 基本上就是根据配置来返回对应的嵌入模型对象，主要有 OpenAI、HuggingFace 和 Local 三种模型可以选择
    }

    @Bean
    @Lazy
    @ConditionalOnMissingBean
    ModerationModel moderationModel(LangChain4jProperties properties) {
        // langchain4j 提供的一种 langchain 定义的三种模型类型之外的模型类型
        // 参考 OpenAI 的 Moderation Model: The Moderation models are designed to check whether 
        // content complies with OpenAI's usage policies. 
        // The models provide classification capabilities that look for content in the following 
        // categories: hate, hate/threatening, self-harm, sexual, sexual/minors, violence, and violence/graphic.
    }
}
```

langchain4j 提供了上述几种模型的自动装配 Bean。通过在 properties 文件或 yaml 文件中提供相关配置我们可以快速的使用这些模型对象。接下来我们以订票场景为例子来介绍如何快速的使用 langchain4j-spring-boot-starter 构建订票系统的客服机器人（使用 Chains component 中的 Agent 来进行构建）。

首先我们可以实现我们自己的 `retriever`，来获取我们所需要的数据。

```java
@Bean
Retriever<TextSegment> retriever(EmbeddingStore<TextSegment> embeddingStore, EmbeddingModel embeddingModel) {

    // You will need to adjust these parameters to find the optimal setting, which will depend on two main factors:
    // - The nature of your data
    // - The embedding model you are using
    int maxResultsRetrieved = 1;
    double minScore = 0.9;

    return EmbeddingStoreRetriever.from(embeddingStore, embeddingModel, maxResultsRetrieved, minScore);
}
```

接着我们需要注册对应的 agent，来作为与用户沟通的角色，我们可以通过 SystemMessage 来自定义一些系统指令。下面是 `CustomerSupportAgent.java` 的实现，其中定义了 `chat()` 方法来接受用户输入，并返回模型的输出。通过 `@SystemMessage` 来定义系统指令。

```java
interface CustomerSupportAgent {

    @SystemMessage({
            "You are a customer support agent of a car rental company named 'Miles of Smiles'.",
            "Before providing information about booking or cancelling booking, you MUST always check:",
            "booking number, customer name and surname.",
            "Today is {{current_date}}."
    })
    String chat(String userMessage);
}
```

langchain4j 提供了 `AiServices` 来快速构建 agent, `bookingTools` 是订票系统的工具，其中提供了一些操作相关数据的接口。通过对应的模型、数据获取接口、聊天上下文存储（即 Memory）设置我们可以得到对应的 agent。

`BookingTools.java`：

```java
@Component
public class BookingTools {

    @Autowired
    private BookingService bookingService;

    @Tool
    public Booking getBookingDetails(String bookingNumber, String customerName, String customerSurname) {
        System.out.println("==========================================================================================");
        System.out.printf("[Tool]: Getting details for booking %s for %s %s...%n", bookingNumber, customerName, customerSurname);
        System.out.println("==========================================================================================");

        return bookingService.getBookingDetails(bookingNumber, customerName, customerSurname);
    }

    @Tool
    public void cancelBooking(String bookingNumber, String customerName, String customerSurname) {
        System.out.println("==========================================================================================");
        System.out.printf("[Tool]: Cancelling booking %s for %s %s...%n", bookingNumber, customerName, customerSurname);
        System.out.println("==========================================================================================");

        bookingService.cancelBooking(bookingNumber, customerName, customerSurname);
    }
}
```

`CustomerSupportAgent` 的定义：

```java
@Bean
CustomerSupportAgent customerSupportAgent(ChatLanguageModel chatLanguageModel, 
    BookingTools bookingTools, 
    Retriever<TextSegment> retriever) {
    return AiServices.builder(CustomerSupportAgent.class)
            .chatLanguageModel(chatLanguageModel)
            .chatMemory(MessageWindowChatMemory.withMaxMessages(20))
            .tools(bookingTools)
            .retriever(retriever)
            .build();
}
```

然后我们就可以使用 agent 来构建我们的应用程序：

```java
@SpringBootTest
class CustomerSupportApplicationTest {

    @Autowired
    CustomerSupportAgent agent;

    @Test
    void should_provide_booking_details_and_explain_why_cancellation_is_not_possible() {

        // Please define API keys in application.properties before running this test.
        // Tip: Use gpt-4 for this example, as gpt-3.5-turbo tends to hallucinate often and invent name and surname.

        interact(agent, "Hi, I forgot when my booking is.");
        interact(agent, "123-457");
        interact(agent, "I'm sorry I'm so inattentive today. Klaus Heisler.");
        interact(agent, "My bad, it's 123-456");

        // Here, information about the cancellation policy is automatically retrieved and injected into the prompt.
        // Although the LLM sometimes attempts to cancel the booking, it fails to do so and will explain
        // the reason why the booking cannot be cancelled, based on the injected cancellation policy.
        interact(agent, "My plans have changed, can I cancel my booking?");
    }

    private static void interact(CustomerSupportAgent agent, String userMessage) {
        System.out.println("[User]: " + userMessage);
        String agentAnswer = agent.chat(userMessage);
        System.out.println("[Agent]: " + agentAnswer);  
    }
}
```

这个测试程序的输出大概是这个样子：

```bash
[User]: Hi, I forgot when my booking is.
[Agent]: Sure, I can help you with that. Can you please provide me with your booking number, customer name, and surname?
[User]: 123-457
[Tool]: Getting details for booking 123-457 for John Smith...
[Agent]: I apologize, but I couldn't find any booking with the provided information. Could you please double-check the booking number, customer name, and surname?
[User]: I'm sorry I'm so inattentive today. Klaus Heisler.
[Tool]: Getting details for booking 123-457 for Klaus Heisler...
[Agent]: I apologize for the inconvenience, but I couldn't find any booking with the provided information. It's possible that there might be a mistake in the booking number or customer details. Could you please double-check and provide the correct booking number, customer name, and surname?
[User]: My bad, it's 123-456
[Tool]: Getting details for booking 123-456 for Klaus Heisler...
[Agent]: Thank you for providing the correct information. I found your booking details.

Booking Number: 123-456
Customer Name: Klaus Heisler
Booking Dates: From 2023-08-14 to 2023-08-16

If you have any further questions or need assistance, feel free to ask.
[User]: My plans have changed, can I cancel my booking?
[Tool]: Cancelling booking 123-456 for Klaus Heisler...
[Agent]: I apologize, but according to our cancellation policy, bookings with a duration of less than 3 days are not eligible for cancellation. Therefore, I'm afraid your booking cannot be canceled.

If you have any other questions or need further assistance, please let me know.
```

## Reference

1. [LangChain Document](https://docs.langchain.com/docs/)
2. [ChatGPT Prompt Engineering for Developers](https://www.bilibili.com/video/BV1514y1Z7z8/)
3. [awesome-chatgpt-prompts](https://github.com/f/awesome-chatgpt-prompts)
4. [langchain4j](https://github.com/langchain4j/langchain4j)
5. [LangChain: Introduction and Getting Started](https://www.pinecone.io/learn/series/langchain/langchain-intro/)
