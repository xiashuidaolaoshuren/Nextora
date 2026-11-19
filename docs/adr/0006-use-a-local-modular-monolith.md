# Use a local modular monolith

The initial workspace is a single Spring Boot modular monolith backed by PostgreSQL and deployed locally with Docker Compose, rather than separate services or a message queue. This fits a solo project and keeps the first deployment manageable while preserving module boundaries for later work.

A React and TypeScript frontend uses an OpenAPI-generated client. User-owned records are scoped to a user through a current-user seam, initially backed by a single local user; sign-in and hosted multi-user deployment are deferred. The model gateway exposes task-shaped operations behind a provider interface, with Claude as the initial default, so domain modules do not depend directly on a provider client.
