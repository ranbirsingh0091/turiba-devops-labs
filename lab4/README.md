# Lab 4 Write-up

## Repository Information
- **GitHub Username**: ranbirsingh0091
- **Repository**: turiba-devops-labs

## Service Overview
- **web**: Nginx web server publishing port 8080 to internal 80.
- **api**: Application service dependent on the database.
- **db**: PostgreSQL database service using named volume `db-data`.

## Security & Configuration
- Passwords and database credentials are fully parameterized through `.env` and `.env.example`.
