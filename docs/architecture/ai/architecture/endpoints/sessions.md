# Get Sessions

**URL**: `/sessions` **Method**: `GET` **Description**: Retrieves a list of chat sessions for a specific user, sorted by start time (newest first).

1. Validates `userId` is present.
2. Queries the database for ChatSessionEntity where `user.username` matches.
3. Sorts results by `startedAt` in descending order (newest first).
4. Maps entities to ChatSessionDTO.

### Query Parameters

|Parameter|Type|Description|Required|
|---|---|---|---|
|`userId`|`String`|The username to filter sessions by.|Yes|

### Response Body (`List<ChatSessionDTO>`)

Returns an array of session objects.

|Field|Type|Description|
|---|---|---|
|`sessionId`|`String`|Unique ID of the session.|
|`title`|`String`|Display title of the chat (derived from first message).|
|`startedAt`|`LocalDateTime`|Timestamp when the session was created.|

**Example JSON**:
``` JSON
[
  {
    "sessionId": "550e8400-e29b-41d4-a716-446655440000",
    "title": "Status of ticket JNG-123...",
    "startedAt": "2023-10-27T10:00:00"
  },
  {
    "sessionId": "728g8400-x29b-41d4-b999-123455440000",
    "title": "New Chat",
    "startedAt": "2023-10-26T14:30:00"
  }
]
```
