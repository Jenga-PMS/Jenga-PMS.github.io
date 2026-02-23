# **Architecture**

## **Overview**
An overview of our system architecture.

![alt text](image.png)

---

## **Backend**

Documentation for the Jenga backend can be found [here](backend/overview/)

---

## **Frontend**

Details about the user interface, web/mobile clients, and state management.

---

## **Deployment**

Information on our CI/CD pipelines, infrastructure (like Kubernetes), and hosting.

---

## **AI**

Our AI architecture is built around the **Model Context Protocol (MCP)**, an open standard that allows AI models to safely interact with external tools and data.

Our main Quarkus application itself *acts as the MCP Server*. This is a powerful design choice because it means our AI agent has secure, direct, and high-performance access to all the project management data (tickets, users, etc.) from the *inside*. There is no slow or complex middle-man service.

### Why We Chose This Architecture

* **Speed:** By having the MCP Server built *into* the main Quarkus application, the AI can access project data instantly.
* **Simplicity:** We don't have to maintain a separate AI service, database connections, or API. It's all part of one unified Quarkus application.
* **Standardization:** Using the MCP standard means our architecture is modular. While we use Gemini today, we could (in theory) connect any MCP-compatible client to our server.

### Core Components

1.  **Quarkus (as the MCP Server):** Our backend is more than just a web application; it's also a compliant Model Context Protocol (MCP) Server, thanks to the `quarkus-mcp-server` extension. This server's job is to define and expose "tools" (like `getTicketDetails` or `findUser`) that an AI model can use.

2.  **Langchain:** Inside our MCP Server, we use the Langchain framework. When a request comes in, Langchain is the "glue" that chains together the necessary steps. For example, it might:
    * Parse the user's request.
    * Call one of the tools (e.g., `getTicketDetails`).
    * Use the tool's result to build a new prompt.
    * Finally, send that prompt to the AI model.

3.  **Gemini API:** This is the external Large Language Model (LLM) that Langchain communicates with. We send it a well-structured prompt, and it provides the generative AI and reasoning capabilities to produce the final answer.

### How It Works (The Flow)

1.  **Request:** A user's request is received by the Quarkus application.
2.  **Process (MCP Server):** The request is routed to the MCP Server module. **Langchain** takes over, calling internal functions (the "tools") to gather all necessary project data (e.g., ticket #123 details).
3.  **Respond (Gemini):** Langchain uses this data to build a final, context-rich prompt and sends it to the **Gemini API**. The AI's response is then sent back through the chain to the user.
