# Architecture & Layers

## Architecture
The backend uses the **Controller-Service-Repository pattern**. This pattern consist of different layers. Each layer has a different task.

``` mermaid
block-beta
  columns 5
  A["Controller"]:5
  Arrow1<["&nbsp;&nbsp;&nbsp;"]>(down):5
  B["Service"]:3 Arrow4<["&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"]>(x):1  M["Mapper"]:1
  Arrow2<["&nbsp;&nbsp;&nbsp;"]>(down):5
  C["JPA-Repository"]:3  Arrow5<["&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"]>(x):1 E["Models"]:1
  Arrow3<["&nbsp;&nbsp;&nbsp;"]>(down):5
  D[("Database")]:5

  style Arrow1 fill:#D3D3D3,stroke:#666666
  style Arrow2 fill:#D3D3D3,stroke:#666666
  style Arrow3 fill:#D3D3D3,stroke:#666666
  style Arrow4 fill:#D3D3D3,stroke:#666666
  style Arrow5 fill:#D3D3D3,stroke:#666666
```

## Layers

### Controller
This is the entry point of the application. It mainly uses **Jakarta RESTful Web Services** (JAX-RS) annotations. These resources handle incoming HTTP requests and call the appropriate methods of the service layer to perform the actual business logic.

``` java title="Example"
@Path("/api/tickets")
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public class TicketResource {

    @Inject // (1)!
    TicketService ticketService;

    @POST // (2)!
    @Path("/{projectId}") // (3)!
    public TicketResponseDTO createTicket(@PathParam("projectId") String projectId, TicketRequestDTO ticketRequestDTO) {
        return ticketService.create(projectId, ticketRequestDTO); // (4)!
    }
}
```

1. Injecting service in controller layer to access business logic
2. Specify the HTTP-method of the endpoint
3. Specify the name of the endpoint
4. Call method of the service layer for the actual business logic

### Service
Service layer is responsible for the actual business logic. It performs calculations, validations independent of how the the actual incoming request was served. It uses mappers to convert between DTOs and Entities. The purpose is to decouple how data is persisted and how it is served through the API. It also protects sensitive data from being exposed.

This ensures:

- [x] **Decoupling:** Separates internal database logic from the external interface
- [x] **Security:** Prevents internal fields (e.g., passwords) from being exposed
- [x] **Stability:** Ensures database changes don't break the public API

``` java title="Example"
@ApplicationScoped
public class TicketService {
    @Inject
    private final TicketRepository ticketRepository;

    @Transactional
    public TicketResponseDTO create(String projectId, TicketRequestDTO ticketRequestDTO) {
        Ticket ticket = ticketMapper.ticketRequestDTOToTicket(ticketRequestDTO); // (1)!
        // Here possibly more logic to be performed
        ticketRepository.persist(ticket); // (2)!
        return ticketMapper.ticketToTicketResponseDTO(ticket); // (3)!
    }
}
```

1. Turn the DTO of the request into a entity, mapping it to a database model
2. Call method of repository layer to save entity
3. Return response DTO

To retreive data of the database, the service layer is accessing the repository layer.

### Repository
The repository layer abstracts all database interaction. It sole purpose is to perform operations on the database. It uses Jakarta Persistence (JPA) to abstractes from the complexity of SQL or JPA queries and database drivers (JDBC) from the service layer. It remains database-independent, unless dialect-specific native queries are used.

Panache sits on top of this layer to reduce boilerplate code. With Panache, it is possible to write a short expression like `find("project.id", projectId)` and it will automatically generates the corresponding SQL query. It aims to reduce boilerplate-code.

``` java title="Example"
public class TicketRepository implements PanacheRepository<Ticket> {
    // Find all tickets of a project
    public List<Ticket> findByProjectId(String projectId) {
        return find("project.id", projectId).list();
    }
```

## Source Code
The architecture is directly reflected on to the soure code structure

``` yaml title="Structure of backend source code" hl_lines="6-13"
└── src
    └── main
        ├── docker
        ├── java
        │   └── org
        │       └── jenga
        │           ├── db # (1)!
        │           ├── dto # (2)!
        │           ├── exception # (3)!
        │           ├── mapper # (4)!
        │           ├── model # (5)!
        │           ├── rest # (6)!
        │           ├── service # (7)!
        └── resources
```

1. Repository for database access
2. Data Transfer Objects
3. Custom exceptions, useful for endpoints to return more information on error
4. Converting between DTOs and entities
5. Business entities, used by JPA for persistance in the database
6. API handling incoming HTTP requests
7. Business logic
