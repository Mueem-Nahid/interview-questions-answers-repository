# Full-Stack & Architecture Interview Questions & Answers

## Backend Integration

**1. How would you design a RESTful API for a Next.js application? What are the best practices?**

- Use `/api` routes for backend logic or a separate backend service.
- Follow REST conventions: resource-based URLs, proper HTTP verbs, status codes.
- Validate input, handle errors, and secure endpoints (auth, rate limiting).
- Use middleware for shared logic (auth, logging).
- Document the API (OpenAPI/Swagger).

**2. Compare REST vs. GraphQL for a Next.js application. When would you use each?**

- **REST:** Simple, cacheable, well-understood, good for standard CRUD.
- **GraphQL:** Flexible queries, reduces over/under-fetching, single endpoint, introspection.
- Use REST for simple APIs, legacy systems, or when caching is critical.
- Use GraphQL for complex data needs, multiple clients, or when you want to minimize requests.

**3. How do you handle authentication in a Next.js app (JWT, OAuth, sessions)?**

- Use libraries like `next-auth` for OAuth, JWT, and session management.
- Store JWTs in HTTP-only cookies for security.
- For SSR, validate tokens in `getServerSideProps` or middleware.
- For API routes, check tokens in headers or cookies.

---

## Database & State Management

**1. How would you structure a full-stack application using Next.js with a database like PostgreSQL or MongoDB?**

- Use `/api` routes or a separate backend for database access.
- Use an ORM (Prisma, TypeORM, Mongoose) for data modeling.
- Keep database credentials secure (env vars).
- Use server components or SSR for data fetching when needed.
- Example structure:
  - `/pages` or `/app`: UI
  - `/api`: API endpoints (CRUD)
  - `/prisma` or `/models`: ORM schemas

**2. Explain caching strategies for a Next.js app (client-side, server-side, CDN).**

- **Client-side:** SWR/React Query for data caching, browser cache.
- **Server-side:** In-memory cache (Redis), HTTP cache headers.
- **CDN:** Cache static assets, images, and SSG pages at the edge.
- Use `revalidate` in ISR for background regeneration.

**3. How do you handle real-time updates in Next.js (WebSockets, Server-Sent Events)?**

- Use WebSockets (Socket.IO, ws) for bidirectional real-time communication.
- Use Server-Sent Events (SSE) for one-way server-to-client updates.
- Integrate with `/api` routes or a separate Node.js server.
- Use client libraries to subscribe to updates and update UI state.

---

## Microservices & Monoliths

**1. When would you choose a microservices architecture over a monolith for a Next.js app?**

- Choose microservices for large teams, independent deployments, scalability, or when different parts of the system have different requirements.
- Use a monolith for small teams, simple apps, or when rapid iteration is needed.
- Consider micro-frontends if splitting the UI is required.

**2. How do you ensure consistency between frontend and backend types in a full-stack TypeScript app?**

- Share type definitions in a common package (e.g., `@myorg/types`).
- Use code generation tools (GraphQL Codegen, tRPC).
- Use OpenAPI/Swagger with codegen for REST APIs.
- Enforce type checks in CI/CD.
