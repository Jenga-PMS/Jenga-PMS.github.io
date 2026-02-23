### MCP (Model Context Protocol)

**What is MCP?**

MCP is an open standard protocol that standardizes the connection between AI models (clients) and external data sources or tools (servers).

* **Architecture:** It operates on a Client-Host-Server model.
* **Host:** The application running the LLM (e.g., Claude Desktop, IDEs, or our custom application).
* **Server:** A standalone service that exposes specific data (Postgres, File System, Git) or capabilities via a standardized API.
* **Protocol:** Defines the message format for resource discovery, prompt templates, and tool invocation.

**What is it used for?**

* **Context Retrieval:** Dynamically fetching relevant context from external systems (logs, documentation, database records) to inject into the LLM's context window.
* **Tool Execution:** Exposing local or remote system commands that the LLM can invoke deterministically.
* **Interoperability:** Decoupling the data integration layer from the specific AI provider.

**Why MCP?**

* **Standardization (Solving the m×n problem):** Instead of building bespoke API integrations for every model provider (OpenAI, Anthropic, Gemini), we build a single MCP-compliant server. Any MCP-compliant client can then consume this data.
* **Security & Scoping:** MCP servers run in their own process. This creates a security boundary where we explicitly define accessible resources, preventing the LLM from having unrestricted access to the host machine or backend.

---

### LangChain4j

**What is it?**

LangChain4j is a high-level Java abstraction layer for LLM application development. It is a native Java implementation of concepts popularized by Python libraries like LangChain and LlamaIndex, designed specifically for the JVM ecosystem.

**Why did I choose it?**

* **Tech Stack Consistency:** It integrates directly with our existing Java infrastructure (Quarkus), eliminating the operational overhead of maintaining a separate Python/Node.js microservice for AI logic.
* **Type Safety:** Java's static typing reduces runtime errors when handling structured outputs from LLMs (e.g., deserializing JSON responses into POJOs).
* **Vendor Agnosticism:** It abstracts the underlying API calls, allowing us to switch model providers without refactoring business logic.

**Core features:**

1. **Unified APIs:**
    * Provides a standardized interface (`ChatLanguageModel`, `EmbeddingModel`) across different providers.
    * This abstraction enables "hot-swapping" of models (e.g., moving from OpenAI GPT-4 to a local Ollama instance) via configuration changes rather than code rewrites.

2. **Toolbox for LLM integration:**
    * **Tools (Function Calling):** Uses the `@Tool` annotation to expose Java methods to the LLM. The framework handles the serialization of method signatures into the LLM's schema (e.g., OpenAI Functions API) and the subsequent execution of the method when selected by the model.
    * **Agents (AI Services):** The `AiServices` API creates dynamic proxies that orchestrate the interaction between the LLM, chat memory, and tools. This implements the "ReAct" (Reasoning and Acting) pattern, allowing the model to autonomously plan and execute tasks.
    * **Chat Memory Management:** Components like `MessageWindowChatMemory` or `TokenWindowChatMemory` manage the context window. They handle the persistence and eviction of conversation history to ensure the payload stays within the model's token limits.

**What do we use?**

* **Orchestration:** `AiServices` (Interface-based declarative agent definition).
* **Context Management:** `MessageWindowChatMemory` (Sliding window implementation for conversation history).
* **Model Implementation:** `gemini-2.5-flash`
* **Data Structures:** `UserMessage`, `AiMessage`, `SystemMessage` for structured prompt engineering.
