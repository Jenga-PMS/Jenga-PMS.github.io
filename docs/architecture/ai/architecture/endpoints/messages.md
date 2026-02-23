# Get Session Messages

**URL**: `/sessions/{sessionId}/messages` **Method**: `GET` **Description**: Retrieves the full message history for a specific chat session.

1. Uses `DatabaseChatMemoryStore` to fetch ChatMessage objects associated with the `sessionId`.
2. Maps each internal ChatMessage to a ChatMessageDTO:
    - `UserMessage` -> `USER`
    - `AiMessage` -> `AI`
    - `SystemMessage` -> `SYSTEM`
    - Other -> `TOOL`

### Path Parameters

|Parameter|Type|Description|Required|
|---|---|---|---|
|`sessionId`|`String`|The UUID of the chat session.|Yes|

### Response Body (`List<ChatMessageDTO>`)

Returns an array of message objects representing the conversation history.

|Field|Type|Description|
|---|---|---|
|`type`|`String`|Type of message: `USER`, `AI`, `SYSTEM`, or `TOOL`.|
|`content`|`String`|The text content of the message.|

**Example JSON**:
``` JSON
[
  {
    "type": "USER",
    "content": "Hello, can you help me?"
  },
  {
    "type": "AI",
    "content": "Yes, I am Jenga. How can I assist you with your tickets?"
  }
]
```
