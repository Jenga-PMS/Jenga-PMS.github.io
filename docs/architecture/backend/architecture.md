# Architecture
The backend is split in different layers:

``` mermaid
block-beta
  columns 5

  A["Controller"]:5
  space:5
  B["Service"]:4 M["Mapper"]:1
  space:5
  C["JPA-Repository"]:4 E["Models"]:1
  space:5
  D[("Database")]:5

  A --> B
  B --> C
  C --> D
```

## Controller

## Service

## Repository

## Database

Explain each architecture
-eg panache sample code also for rest sample request (Request and Response tabbed block)


## Source Code
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
