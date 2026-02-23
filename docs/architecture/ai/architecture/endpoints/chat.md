# Chat Endpoint

**URL**: `/chat` **Method**: `POST` **Description**: Sends a message to the AI, processes it (potentially executing tools), and returns the AI's response. manages session creation and updates automatically.

1. **Session Handling**:
	- Checks if `conversationId` is provided. If not, generates a new UUID.
	- Calls ensureSessionExists:
		- Checks if a ChatSessionEntity exists for the ID.
		- If not, creates a new ChatSessionEntity.
		- Sets `user`, `projectId`, and `startedAt`.
		- Generates a `title` from the first 30 characters of the initial message (or "New Chat" fallback).
		- Persists the session.
2. **Context Setup**:
    - Sets ChatRequestContext with user and project details for use by Tools (e.g., specific permission checks or scoping searches).
3. **AI Execution**:
    - Invokes `AiService.chat()`.
    - AiService may autonomously execute tools (e.g., `searchTickets`, `createTicket`) based on the message.
    - Catches exceptions (e.g., tool failures) and returns a friendly error message if needed.

### Request Body (ChatRequestDTO)

|Field|Type|Description|Required|
|---|---|---|---|
|`message`|`String`|The text message to send to the AI.|Yes|
|`conversationId`|`String` (UUID)|The ID of the current conversation. If empty/null, a new session is created.|No|
|`currentUser`|`String`|Username of the user sending the message.|Yes|
|`currentProjectID`|`Long`|ID of the project context (for checking tickets, etc.).|No|
|`currentTicketID`|`Long`|ID of the specific ticket context (if applicable).|No|

**Example JSON**:
``` JSON
{
  "message": "What is the status of ticket JNG-123?",
  "conversationId": "550e8400-e29b-41d4-a716-446655440000",
  "currentUser": "johndoe",
  "currentProjectID": 101
}
```

### Response Body (ChatResponseDTO)

|Field|Type|Description|
|---|---|---|
|`response`|`String`|The text response from the AI.|
|`conversationId`|`String` (UUID)|The ID of the conversation (useful if a new one was created).|

**Example JSON**
``` JSON
{
  "response": "The current status of ticket JNG-123 is 'In Progress'. It is currently assigned to Sarah Smith.",
  "conversationId": "550e8400-e29b-41d4-a716-446655440000"
}
```
