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

## AI Agent Goals

### Ticket Management

- **Ticket Discovery** – Natural language search across tickets (e.g. "find all open bugs assigned to me blocking the release")
- **Smart Ticket Creation** – Draft tickets from a brief prompt, auto-suggesting title, description, priority, size, labels, and acceptance criteria
- **Bulk Operations** – Reassign, re-label, close, or update status across multiple tickets in a single command
- **Ticket Summarisation** – Summarise long ticket descriptions, comment threads, or entire sprints into a concise overview

### Planning & Analysis

- **Implementation Roadmaps** – Break a high-level feature or epic into ordered, actionable tickets with dependencies
- **Acceptance Criteria Generation** – Derive structured acceptance criteria from a feature description
- **Effort & Risk Assessment** – Suggest ticket size/priority and flag potential blockers or missing context

### General AI Capabilities

- **Contextual Q&A** – Answer questions about the project using existing ticket data (e.g. "what was the reason we dropped X?")
- **Web Search Integration** – Look up external context (docs, RFCs, issues) to enrich answers or tickets
- **Natural Language Commands** – Perform any supported action via free-text instructions

### Non-Functional Requirements

- **Persistent Sessions** – Maintain conversation context across multiple interactions so users don't have to repeat themselves
- **Authorisation Enforcement** – The agent can only act within the scope of the authenticated user's permissions; no destructive or cross-project actions without explicit confirmation
- **Auditability** – Every agent-driven change is attributable and reversible

---

# Atlassian Agentic AI

Atlassian uses a few overlapping names:
- **Rovo in Jira / Rovo Agents**: “agent” experiences embedded into Jira that can help you search, summarize, draft, and (in some setups) take actions like creating/updating work items. ([atlassian.com](https://www.atlassian.com/software/jira/ai?utm_source=chatgpt.com "Rovo in Jira: AI features | Atlassian"))
- **Atlassian Intelligence**: the umbrella for AI features across Jira/JSM/Confluence (summaries, writing help, knowledge features, etc.). ([atlassian.com](https://www.atlassian.com/blog/artificial-intelligence/ai-jira-issues?utm_source=chatgpt.com "Get out of tickets and into your work faster with AI in Jira - Atlassian"))
- **Jira Service Management Virtual Service Agent**: a support-focused chatbot for deflecting/automating Tier-1 help requests across channels. ([atlassian.com](https://www.atlassian.com/software/jira/service-management/features/itsm/virtual-agent?utm_source=chatgpt.com "AI Support: Jira Service Management Virtual Service Agent | Atlassian"))

---

## Capabilities

### 1. Speed up reading & triage
- Summarize issues / long comment threads 
- Summarize linked context

### 2. Drafting + rewriting work
- In editors (Jira descriptions, Confluence pages), you can use an agent to **draft, rephrase, review, or standardize tone/structure**. 
- In JSM specifically, AI can help **create/edit knowledge base articles** from an issue context. 

### 3. Search
- Rovo positions itself as **enterprise search + context-aware help** across Atlassian content. 
- There are also point features like **JQL error fixing** to help people who don’t live in JQL daily. 

### 4. Support automation via Virtual Service Agent (ITSM / helpdesk use)
- A conversational agent for **24/7 self-service** that can deflect repetitive Tier-1 questions. 

### 5. Using Jira through ChatGPT
- Atlassian has been pushing connectors so you can **summarize, create/manage issues, and automate workflows in natural language** from ChatGPT (via their Rovo MCP ecosystem).