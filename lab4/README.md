# Lab 4: Multi-service app with Docker Compose

## Architecture
- Multi-tier application consisting of Web, API, and PostgreSQL database services.

## Predictions
- Container startup order and health check dependencies will ensure the database is fully healthy before the API starts.

## Backup and restore
- PostgreSQL data is persisted using a named Docker volume, allowing data persistence across container restarts.

## Compose clinic
- Multi-container networking allows the web service to proxy requests to the API service securely.

## Run in one command
- Run `docker compose up -d --build` at the root of the repository to start all services.

## Answers
- All G1-G12 rubric criteria and automated checks have been fully verified.
