# Extrinsic Tools
Extrinsic means, the tools are primarily being called by the Agent for the User. This is usually the execution of the task given by the user.

### CreateTicketTool

This tool handles the logic of assembling and persisting a new ticket directly through repositories.

- **Context Resolution**: It first checks if `projectId`, `reporterUsername`, and `assigneeUsername` were provided by the AI. If not, it falls back to the `ChatRequestContext` to fetch the user's current active project and username.    
- **Entity Initialization**: It instantiates a new `Ticket` entity, sets the title, description, and hardcodes the initial status to `OPEN`. It then looks up the `Project` and `User` entities (for reporter and assignee) from their respective repositories and links them to the ticket.
- **Label Processing**: If a comma-separated string of labels is provided, it splits the string and iterates through each label name. It queries the `LabelRepository` to see if the label already exists for that specific project; if it doesn't, it instantiates a new `Label`, persists it, and attaches it to the ticket. 
- **Acceptance Criteria Processing**: If a semicolon-separated string of criteria is provided, it splits it, creates an `AcceptanceCriteria` object for each item (defaulting `completed` to false), and links them to the new ticket.
- **Number Generation & Persistence**: It queries the `TicketRepository` for the current maximum ticket number in the project, increments it by 1, and assigns it to the new ticket before persisting the entity to the database.

### DeleteTicketTool

This tool provides simple deletion capabilities.

- **ID Resolution**: It resolves the `ticketId` from the AI's input or the `ChatRequestContext`.
- **Validation**: It first calls `ticketService.findById` to ensure the ticket actually exists before attempting deletion.
- **Execution**: It calls `ticketService.delete` with the verified ID and returns a success string.

### EditTicketTool

This tool performs partial updates on an existing ticket.

- **ID Resolution**: Like other tools, it attempts to use the provided `ticketId` or falls back to the `ChatRequestContext`.
- **State Hydration**: It fetches the existing ticket's current state via `ticketService.findById`.
- **DTO Construction**: It creates a `TicketRequestDTO` and pre-fills it with the _existing_ ticket's values.
- **Selective Overwrite**: It checks each parameter provided by the AI (title, description, priority, etc.). If a parameter is not null, it overwrites the corresponding value in the DTO.
- **Assignee Clearing Logic**: It includes specific logic for the assignee field: if the AI passes the string "unassigned" or a blank string, it explicitly sets the assignee field to `null` in the DTO to clear it.
- **Execution**: It passes the finalized DTO to `ticketService.update` to apply the changes.

---

# Intrinsic Tools
Intrinsic means, the tools are primarily being called by the Agent for the Agent. This usually happens to acquire necessary knowledge to complete the task or answer the question.

### AskAboutTicketTool

This tool retrieves and formats ticket details for the AI to read.

- **ID Resolution**: It resolves the ticket ID from the input or the `ChatRequestContext` unless specified otherwise.
- **Data Retrieval**: It calls `ticketService.findById` to get a `TicketResponseDTO`.
- **Formatting**: It constructs a highly readable string containing the project name, ticket number, title, status, assignee (defaulting to "Unassigned" if null), and description.
- **Response Generation**: It wraps the formatted string and the ticket ID into an `AskAboutTicketResponseDTO` and returns it.

### SearchTicketTool

This tool dynamically builds a search query.

- **DTO Initialization**: It initializes a `TicketSearchDTO` and a nested `Filter` object.
- **Base Mapping**: It maps the base text search parameters (`query`, `title`, `description`, `projectId`) directly to the DTO and applies a default result limit of 5 if the AI didn't specify one.
- **Conditional Filtering**: It checks the provided lists for priorities, statuses, sizes, assignees, reporters, and labels. If any of these lists are not null and not empty, it adds them to the nested `Filter` object.
- **Execution**: It passes the constructed DTO to `ticketService.searchTickets` and returns the resulting list of tickets directly.

### GetAllProjectsTool & GetAllUsersTool

These are simple discovery tools used to feed valid system identifiers to the AI.

- **Projects**: Calls `projectService.findAll()`, maps the results into a formatted bulleted list containing the Project ID and Name, and returns it as a single string.
- **Users**: Calls `userService.findAll()`, maps the results to extract just the usernames, sorts them alphabetically, and joins them into a single comma-separated string.

### WebSearchTool (currently broken, see [Problems](../problems.md))

This tool handles external data gathering.

- **Configuration Check**: It first validates that the `google.api.key` and `google.cse.id` are configured in the environment properties; if not, it aborts and returns an error message.
- **API Invocation**: It passes the query, API key, and Engine ID to the `@RestClient` interface (`GoogleSearchApi`), which executes the HTTP request.
- **Result Parsing**: It iterates through the items in the returned `WebSearchResponseDTO` and maps each one to a multi-line string containing the Title, Snippet, and URL, returning the mapped list to the AI.
    