# ImportService

`org.jenga.service.ImportService`

Handles bulk import of tickets into a Jenga project from two different sources: **GitHub Issues** and the **Jenga native export format**.

Both methods are transactional and return an [`ImportReportDTO`](#return-value-importreportdto) that summarises the result.

---

## Methods

### `importFromGitHub`

```java
public ImportReportDTO importFromGitHub(String projectId, List<GitHubIssueDTO> githubIssues)
```

Converts a list of GitHub issue DTOs into Jenga tickets and persists them under the given project.

**Parameters**

| Parameter      | Type                   | Description              |
| -------------- | ---------------------- | ------------------------ |
| `projectId`    | `String`               | ID of the target project |
| `githubIssues` | `List<GitHubIssueDTO>` | GitHub issues to import  |

**Processing steps (per issue)**

1. Creates a new `Ticket` and sets its title from the issue title.
2. Parses the issue body line by line:
   - Lines starting with `- [ ]` or `- [x]` are parsed as **Acceptance Criteria** (completed state is preserved).
   - All other lines are joined and stored as the ticket **description**.
3. Resolves the **assignee** by matching the first GitHub assignee's login to a Jenga username. If not found, a warning is logged and the ticket is still imported without an assignee.
4. Looks up or creates **Labels** by name within the project, using the GitHub label's color.
5. Maps the GitHub project-board **status** to a `TicketStatus` (see [Status Mapping](#status-mapping)).
6. Persists the ticket.

**Error handling**

- If the project is not found, returns immediately with a fatal error and `0` successful imports.
- Individual ticket failures are caught, logged as warnings, and do not abort the rest of the import.

---

### `importFromJenga`

```java
public ImportReportDTO importFromJenga(String projectId, List<TicketRequestDTO> ticketRequests)
```

Re-imports tickets from a Jenga native export (e.g. a previously exported JSON), preserving all fields.

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `projectId` | `String` | ID of the target project |
| `ticketRequests` | `List<TicketRequestDTO>` | Jenga-format ticket requests |

**Processing steps (per ticket)**

1. Sets all standard fields: title, description, priority, size, status.
2. Looks up or creates **Labels** by name (global, not project-scoped).
3. Resolves the **reporter** by username — **throws** if not found, skipping the ticket and recording the error.
4. Optionally resolves the **assignee** by username — also **throws** if provided but not found.
5. Maps `AcceptanceCriteria` DTOs to entities.
6. Persists the ticket.

**Error handling**

- If the project is not found, returns immediately with an error and `0` successful imports.
- Per-ticket failures (e.g. unknown reporter/assignee) are caught, added to the error list, and do not abort the rest of the import.

> **Note:** Unlike `importFromGitHub`, a missing reporter or assignee is a **hard failure** for that ticket rather than a soft warning.

---

## Status Mapping

Used internally by `importFromGitHub` to convert GitHub project-board column names to `TicketStatus` values.

| GitHub status | Jenga `TicketStatus` |
|---|---|
| `OPEN`, `BACKLOG`, `NO STATUS`, *(absent)* | `OPEN` |
| `IN PROGRESS` | `IN_PROGRESS` |
| `IN REVIEW`, `IN TEST` | `IN_REVIEW` |
| `RESOLVED` | `RESOLVED` |
| `CLOSED`, `DONE` | `CLOSED` |
| `ON HOLD` | `ON_HOLD` |
| *(anything else)* | `OPEN` *(+ warning log)* |

---

## Return value — `ImportReportDTO`

Both methods return an `ImportReportDTO` with:

| Field | Description |
|---|---|
| `successfulImports` | Number of tickets successfully persisted |
| `errors` | List of error/warning messages for failed tickets |
