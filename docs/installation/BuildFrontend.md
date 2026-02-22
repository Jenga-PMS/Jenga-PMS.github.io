# Build Frontend

## Overview
The frontend depends on the backend API contract. Therefore the correct order is:

1. Build/start backend
2. Update OpenAPI spec in frontend
3. Generate frontend API client (`gen:api`)
4. Build or start frontend

## Prerequisites

- Java 21 (for backend build)
- Node.js 20+ and npm
- Repository cloned locally

## 1. Build or Start Backend First
From repository root:

```bash
cd backend
./mvnw package
```

For local development you can run the backend directly:

```bash
cd backend
./mvnw quarkus:dev
```

By configuration, OpenAPI files are written to:

- `backend/target/openapi/openapi.yaml`
- `backend/target/openapi/openapi.json`

## 2. Update Frontend OpenAPI Contract
From repository root, copy backend schema into frontend:

```bash
cp backend/target/openapi/openapi.yaml frontend/jenga/openapi.yaml
```

Alternative (if backend is running):

```bash
curl -sS http://localhost:8080/openapi -o frontend/jenga/openapi.yaml
```

## 3. Generate Typed API Client

```bash
npm --prefix frontend/jenga install
npm --prefix frontend/jenga run gen:api
```

This regenerates `frontend/jenga/src/api`.

## 4. Build Frontend

```bash
npm --prefix frontend/jenga run build
```

Build output is written to `frontend/jenga/dist`.

## 5. Run Frontend in Development

```bash
npm --prefix frontend/jenga run dev
```

Default dev server:

- Frontend: `http://localhost:7000`
- API proxy (`/api`) -> `http://localhost:8080`

## Notes
- If backend endpoints or DTOs change, repeat steps 2-4.
- Do not manually edit generated files in `frontend/jenga/src/api`.
