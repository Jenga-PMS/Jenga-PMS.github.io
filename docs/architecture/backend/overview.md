# Technical Documentation Backend

## Overview
The backend of **Jenga** is responsible for the business logic and persistance of data. It exposes a simple REST-API for interaction, which can be found [here](api.md). Built in Java on Quarkus, Jenga is a cloud-native application benefiting from Quarkus's many advanced features.

Jenga also has an AI-assistant. The relevant documentation for that can be found [here](../ai.md)

## Core Technologies
These are the main dependencies used to build the backend:

- **Programming Language:** [Java 21](https://www.oracle.com/java)
- **Framework:** [Quarkus](https://quarkus.io)
- **API Layer:** [Jakarta REST](https://projects.eclipse.org/projects/ee4j.rest)
- **Persistence:** [Jakarta Persistence (JPA)](https://www.oracle.com/java/technologies/persistence-jsp.html) using [Hibernate](https://hibernate.org/)
- **Security:** [SmallRye JWT](https://quarkus.io/guides/security-jwt)
- **Database:** [PostgreSQL](https://www.postgresql.org/)
- **Build Tool:** [Maven](https://maven.apache.org/)

## Current Features
- [x] **Simple API:** Easy to use REST-API, see API-reference [here](api.md)
- [x] **Projects:** Create projects as basket of tickets, create custom labels to organize tickets 
- [x] **Tickets:** Create tickets with various data fields to describe and manage tickets
- [x] **Users:** Native registration and login
- [x] **Security:** Authentication with JWT
- [x] **Search:** Search endpoint with filter
- [ ] <s>Thousands of lines of commented source code (not just TODO and FIXME)</s>

## Limitations

For not implemented features and limitations see [here](limitations.md)
