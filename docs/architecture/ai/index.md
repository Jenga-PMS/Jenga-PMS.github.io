
## Relevant Code

```
.
├── dto
│   ├── GitHubIssueDTO.java
│   ├── ImportReportDTO.java
│   ├── ImportStatusDTO.java
│   ├── mcpserver
│   │   ├── AskAboutTicketResponseDTO.java
│   │   ├── ChatMessageDTO.java
│   │   ├── ChatRequestDTO.java
│   │   ├── ChatResponseDTO.java
│   │   ├── ChatSessionDTO.java
│   │   ├── SearchResultItemDTO.java
│   │   └── WebSearchResponseDTO.java
├── model
│   ├── ChatMemoryEntity.java
│   ├── ChatSessionEntity.java
│   ├── MessageType.java
├── rest
│   ├── AiResource.java
│   ├── ImportResource.java
├── service
│   ├── ImportService.java
│   ├── mcpserver
│   │   ├── AiService.java
│   │   ├── ChatMemoryProvider.java
│   │   ├── ChatRequestContext.java
│   │   ├── DatabaseChatMemoryStore.java
│   │   └── GoogleSearchApi.java
└── tool
    ├── AskAboutTicketTool.java
    ├── CreateTicketTool.java
    ├── DeleteTicketTool.java
    ├── EditTicketTool.java
    ├── GetAllProjectsTool.java
    ├── GetAllUsersTool.java
    ├── SearchTicketTool.java
    └── WebSearchTool.java
```

## Core Architecture & Data Flow

The agent's logic is managed by a central `AiService` within the Quarkus application. This service orchestrates the entire process, from receiving a user's message to returning a response.

Here is the typical data flow for a single user message:

1.  A user sends a message from the front end. The backend packages this into a `Chat` DTO, which includes the message content and a `Chat ID`.
2.  The `Chat` DTO is passed to the `AiService`.
3.  The `AiService` loads the **System Prompt**, which contains the AI's core instructions, personality, and safety rules.
4.  It then loads the **Persistent Memory** associated with the `Chat ID`, allowing the agent to remember the previous turns of the conversation.
5.  Finally, the service loads the list of **Available Tools** (see below) that the AI is allowed to use.
6.  This complete context (System Prompt + Chat History + Tools + User Message) is processed by Langchain and sent to the **Gemini API**.
7.  The Gemini model decides whether to respond directly or to use one or more of the provided tools.
8.  If a tool is used, the `AiService` executes it, gets the result, and feeds it back into the model.
9.  The model generates its final text response, which is packaged into a `ChatResponse` DTO and sent back to the user.

## The System Prompt: A "Safety-First" Approach

A core feature of our agent is its safety-first workflow, which is strictly enforced by the **System Prompt**.

The AI agent is explicitly forbidden from making changes to your data (creating, editing, or deleting) without your direct approval. It operates on a "propose and confirm" model.

> **Prime Directive: The AI must always ask for confirmation before executing any action that modifies project data.**

This creates a safe, predictable user experience:

1.  **User:** "Can you delete ticket #34?"
2.  **AI (using a knowledge tool):** "Ticket #34 is titled 'Fix login button bug'. Are you sure you want to delete it?"
3.  **User:** "Yes, please."
4.  **AI (using the `Delete Ticket Tool`):** "I have now deleted ticket #34."

## Available Tools

### Knowledge Acquisition Tools (Read-Only)

These tools are used by the agent to gather information and answer your questions. The agent often combines these tools to answer complex requests.

* **List Projects:** Fetches a complete list of all projects.
* **List All Tickets:** Gets a list of all tickets across all projects.
* **List Project Tickets:** Gets a list of tickets for a specific project.
* **List Users:** Fetches a list of all users in the system.
* **Filter Tickets:** A powerful tool that allows the agent to find specific tickets or IDs based on your descriptions (e.g., "all open tickets for the Jenga project," "the bug I filed yesterday about the login page").
* **Get Ticket Details:** Retrieves all information for a single ticket. The agent typically uses this tool *after* using **Filter Tickets** to find the exact ID of the ticket you are asking about.
* **Web Search:** A general-purpose tool to search the public internet for information not contained within Jenga. It does this by directly interfacing with the Google Search API.

### Action Tools (Requires Confirmation)

These tools allow the agent to modify project data on your behalf. As part of our safety-first approach, the AI will *always* ask for your explicit confirmation before using any of these tools.

* **Create Ticket Tool (#32):** Creates a new ticket in a project. The AI will first ask for all required details (project, title, description, etc.).

* **Edit Ticket Tool (#33):** Modifies the fields of an existing ticket. If you make a general request (e.g., "change the priority of the login bug"), the agent will first use the `Filter Tickets` tool to find the correct ticket ID, then ask you to confirm the change.

* **Delete Ticket Tool (#34):** Deletes a ticket from the system. Similar to editing, if you ask to delete a ticket by its name, the agent will first find its ID, confirm the correct ticket with you, and only then execute the deletion.

## Key Design Principle: Service-Based Integration

A critical aspect of our architecture is how the tools are implemented.

The AI agent's tools do **not** query the database directly or create their own DTO packages. Instead, they act just like any other part of our application: they use the **existing, tested backend services** (e.g., `TicketService.deleteTicket(id)` or `ProjectService.getProjects()`).

This design has major benefits:

1.  **Security:** The AI automatically respects all existing business logic, permissions, and validation rules defined in our services.
2.  **Maintainability:** If we update the `TicketService`, the AI's tool is automatically updated. There is no need to rewrite separate AI logic.
3.  **Stability:** We are reusing robust, tested code rather than building new, parallel database queries for the AI.
