# Authentication 

Jenga has built in authentication using JWT (JSON Web Token). It provides own endpoints for handling the authentication logic.

For this it has two endpoints:

- **Registration**: Using the endpoint `/api/auth/register`, a user can be registered using a username and a password
- **Login**: Login username and password using `/api/auth/login`. The endpoint will return a JWT (JSON Web Token). The password is saved as hash using Bcrypt

Following diagram shows a successful login:
``` mermaid
sequenceDiagram
    participant Client
    participant Server
    Client->>Server: POST /api/auth/login (username, password)
    Note over Server: Validate credentials
    Server-->>Client: 200 OK (Issuing JWT)
    Note over Client: Store token
    Client->>Server: GET /api/projects with header Authorization: Bearer <token>
    Note over Server: Verify Signature & Expiration
    Server-->>Client: 200 OK (Data)
```
