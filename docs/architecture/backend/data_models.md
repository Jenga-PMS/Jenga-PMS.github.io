# Data models
As Jenga is a project management tool for issues, everything is organized in projects. It acts as a container for tickets and other project relevant data. Within a project, user can create tickets. Tickets also have different relations with other objects, e.g. a ticket can have comments.

This diagramm shows a simplified version of the relations between business objects:
``` mermaid
erDiagram
    PROJECT ||--o{ TICKET : contains
    PROJECT ||--o{ LABEL: contains
    USER ||--o{ TICKET : creates
    TICKET ||--o{ COMMENT : has
    TICKET ||--o{ ACCEPTANCE_CRITERIA : has
```

In the Jenga code, these relations are established with JPA. Jakarta has different annotations to create relations between different entities.

Here is an example relation between projects and tickets:
=== "Ticket Entity"
    ```java
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "project_id")
    private Project project;
    ```

=== "Project Entity"
    ```java
    @OneToMany(mappedBy = "project", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Ticket> tickets = new ArrayList<>();
    ```
With `@JoinColumn(name = "project_id")`, the actual foreign key column is defined in the database table ticket.


### Full Jenga object relations
The diagram shows relationships of database tables established through primary and foreign keys.
``` mermaid
erDiagram
    PROJECTS ||--o{ TICKETS : "contains"
    PROJECTS ||--o{ LABELS : "has"
    USERS ||--o{ TICKETS : "assigned to"
    USERS ||--o{ TICKETS : "reports"
    USERS ||--o{ COMMENTS : "writes"
    USERS ||--o{ CHAT_SESSIONS : "owns"
    TICKETS ||--o{ ACCEPTANCE_CRITERIAS : "has"
    TICKETS ||--o{ COMMENTS : "has"
    TICKETS ||--o{ TICKET_LABELS : "labeled with"
    LABELS ||--o{ TICKET_LABELS : "assigned to"
    
    TICKETS ||--o{ TICKETS_BLOCKED : "blocks/blocked by"
    TICKETS ||--o{ TICKETS_RELATED : "relates to"

    PROJECTS {
        string id PK
        timestamp createdate
        string description
        timestamp modifydate
        string name
    }

    USERS {
        string username PK
        timestamp createdate
        string email
        timestamp modifydate
        string password
    }

    TICKETS {
        bigint id PK
        timestamp createdate
        text description
        timestamp modifydate
        string priority
        string size
        string status
        bigint ticketnumber
        string title
        string assignee_id FK
        string project_id FK
        string reporter_id FK
    }

    ACCEPTANCE_CRITERIAS {
        bigint id PK
        boolean completed
        string description
        bigint ticket_id FK
    }

    COMMENTS {
        bigint id PK
        string comment
        timestamp createdate
        timestamp modifydate
        string user_id FK
        bigint ticket_id FK
    }

    LABELS {
        bigint id PK
        string color
        string name
        string project_id FK
    }

    TICKET_LABELS {
        bigint ticket_id FK
        bigint label_id FK
    }

    CHAT_SESSIONS {
        string sessionid PK
        bigint projectid
        timestamp startedat
        string title
        string user_id FK
    }

    CHAT_MEMORY {
        bigint id PK
        string memoryid
        text messagejson
        string messagetype
    }

    TICKETS_BLOCKED {
        bigint ticket_id FK
        bigint blocked_ticket_id FK
    }

    TICKETS_RELATED {
        bigint ticket_id FK
        bigint related_ticket_id FK
    }
```
