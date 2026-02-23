![Architecture Diagram](Diagram.svg)

---

## 1. High-level overview

This is a **backend AI chat service** that:

- exposes REST endpoints for **chat** and **session history**
- uses **LangChain4j** in `AiService` to call an LLM (Gemini API)
- supports **tool calling** (tickets, users, projects, web search, etc.)
- persists **conversation memory/history** in **PostgreSQL**

--- 
## 2. Endpoints

- [Chat Endpoint](architecture/endpoints/chat.md)
- [Get Sessions](architecture/endpoints/sessions.md)
- [Get Session Messages](architecture/endpoints/messages.md)

---

## 3. Main runtime components

### API / Controller layer

`AiResource.java (Controller)`

- Entry point for HTTP requests.
- Handles chat requests and session/message retrieval endpoints (shown on the right side).

### Request context

`ChatRequestContext (@RequestScoped)`

- Holds request-scoped data (attached to the current HTTP request), e.g.:

	- `currentUser`
	- `currentProjectID`
	- `currentTicketID`

- This context is used downstream so tools and services know “who/what this chat is about” without passing those parameters everywhere manually.

### Service layer (LLM orchestration)

`AiService.java (LangChain4j)`

- Core orchestration:

    - builds the prompt/messages
    - loads conversation memory
    - calls the LLM API
    - receives tool calls from the LLM and executes them via the Tool System
    - returns the final assistant response

### DTOs (API contracts)

`ChatRequestDTO`

- Input payload for chat:

    - `message`
    - `conversationId`
    - `currentUser`
    - `currentProjectId`
    - `currentTicketId`

`ChatResponseDTO`

- Output payload:

    - `response`
    - `conversationId`

---

## 4. Chat Persistence & Memory

### Message typing

`MessageType` **enum**

- `USER`, `AI`, `SYSTEM`, `TOOL`

This is important because memory stores usually need to reconstruct a conversation with correct roles (what the user said vs. what the assistant said vs. tool outputs).

### Stored entity

`ChatMemoryEntity`

- Represents persisted chat messages:

    - `memoryId`
    - `messageJson`
    - `messageType`

### Memory integration

`ChatMemoryProvider` (configured into LangChain4j)

- Responsible for connecting LangChain4j’s memory mechanism to your storage.

`DatabaseChatMemoryStore`

- Concrete persistence layer for memory:

	- **loads** history for a conversation/session
	- **saves** new messages (user, assistant, tool outputs)

### Database

**PostgreSQL**

- Stores:

	- conversation memory/messages
	- sessions
	- tickets/projects/users/etc. (via repositories)

---

## 5. Tools

### What is it?

- A collection of “capabilities” the LLM can invoke when it needs real data/actions.
- Typical flow:

	  1. The model decides it needs a tool (e.g., “search tickets”).
	  2. LangChain4j emits a **tool call**.
	  3. Your Tool System executes it.
	  4. The tool output is fed back into the model as a `TOOL` message.
	  5. The model then produces a final user-facing answer.

### Available Tools (ToDo create Links)

**Extrinsic**:

Extrinsic means, the tools are primarily being called by the Agent for the User. This is usually the execution of the task given by the user.

- [`CreateTicketTool`](architecture/Tools.md#createtickettool)
- [`DeleteTicketTool`](architecture/Tools.md#deletetickettool)
- [`EditTicketTool`](architecture/Tools.md#edittickettool)

**Intrinsic**:

Intrinsic means, the tools are primarily being called by the Agent for the Agent. This usually happens to acquire necessary knowledge to complete the task or answer the question.

- [`AskAboutTicketTool`](architecture/Tools.md#askaboutticketttool)
- [`SearchTicketTool`](architecture/Tools.md#searchtickettool)
- [`GetAllProjectsTool`](architecture/Tools.md#getalllprojectstool--getalluserstool)
- [`GetAllUsersTool`](architecture/Tools.md#getallprojectstool--getalluserstool)
- [`WebSearchTool`](architecture/Tools.md#websearchtool-currently-broken-see-problems)

---

## 6. End-to-end request flow (chat)

Here’s the typical sequence when the user sends a message:

1. **Frontend →** `AiResource` (`/chat`)
2. Controller builds/validates a `ChatRequestDTO`
3. Controller sets up `ChatRequestContext` (user/project/ticket)
4. Controller calls `AiService`
5. `AiService`:

	- loads prior messages via `ChatMemoryProvider` **→ DatabaseChatMemoryStore → PostgreSQL**
	- calls the **LLM API**

6. If the model requests tools:

	- `AiService` routes the tool call to **Tools System**
	- Tools call **services/repositories**
	- results are returned as `TOOL` messages
	- model continues and produces final response
	
7. `AiService` saves new messages back to PostgreSQL (user + tool + AI)
8. Controller returns `ChatResponseDTO` to the client