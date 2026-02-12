# Architecture & Layers

## Architecture
The backend uses the **Controller-Service-Repository pattern**. This pattern consist of different layers. Each layer has a different

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

1. Injecting a in controller to access business logic
2. Specify the HTTP-method of the endpoint
3. Specify the name of the endpoint
4. Call method of the service layer for the actual business logic

### Service
Service layer is responsible for the actual business logic. This layer also maps between DTOs and Entities

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

1. Turn the DTO of the request into a entity mapping to a database table
2. Call method of repository layer to save entity
3. Return response DTO

### Repository
This layer abstracts all database interaction.

## Source Code
The architecture is directly reflected on to the soure code structure

``` title="Structure of backend source code" hl_lines="6-13"
└── src
    └── main
        ├── docker
        ├── java
        │   └── org
        │       └── jenga
        │           ├── db
        │           ├── dto
        │           ├── exception
        │           ├── mapper
        │           ├── model
        │           ├── rest
        │           ├── service
        └── resources
```
