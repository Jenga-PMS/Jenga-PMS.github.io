# Architecture
The backend is split in different layers:

## Layers

``` mermaid
block-beta
  columns 5
  A["Controller"]:5
  Arrow1<["&nbsp;&nbsp;&nbsp;"]>(y):5
  B["Service"]:3 Arrow4<["&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"]>(x):1  M["Mapper"]:1
  Arrow2<["&nbsp;&nbsp;&nbsp;"]>(y):5
  C["JPA-Repository"]:3  Arrow5<["&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"]>(x):1 E["Models"]:1
  Arrow3<["&nbsp;&nbsp;&nbsp;"]>(y):5
  D[("Database")]:5

  style Arrow1 fill:#D3D3D3,stroke:#666666
  style Arrow2 fill:#D3D3D3,stroke:#666666
  style Arrow3 fill:#D3D3D3,stroke:#666666
  style Arrow4 fill:#D3D3D3,stroke:#666666
  style Arrow5 fill:#D3D3D3,stroke:#666666
```

### Controller

### Service

### Repository

### Database

### Source Code
The architecture is directly reflected on to the soure code structure

``` title="Structure of backend source code"
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
