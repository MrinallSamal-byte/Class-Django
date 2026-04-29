# Class New Record (Final Code Through Chapter 3)

This folder contains the consolidated final project state up to Chapter 3.

## Included

- Full Django codebase from Chapter03
- Docker setup (`Dockerfile`, `docker-compose.yml`)
- Environment variable setup (`.env`, `.env.example`)
- Python dependency list (`requirements.txt`)

## Environment Variables

The app reads these variables via `python-decouple`:

- `DB_HOST`
- `DB_PORT`
- `DB_USER`
- `DB_PASSWORD`
- `DB_NAME`
- `EMAIL_HOST_USER`
- `EMAIL_HOST_PASSWORD`
- `DEFAULT_FROM_EMAIL`

Use `.env.example` as a template.

## Run With Docker

```bash
docker compose up --build
```

The web app will run on `http://localhost:8000`.

## If You Run Postgres Manually From Docker Desktop

Use these values in the Postgres run dialog:

- Container name: `class-new-record-db`
- Host port: `5432`

Then set `.env` as:

- `DB_HOST=localhost`
- `DB_PORT=5432`
- `DB_USER=postgres`
- `DB_PASSWORD=postgres`
- `DB_NAME=postgres`

## Run Locally (without Docker)

1. Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Make sure PostgreSQL is running and update `.env` for your local DB host/user/password.

4. Run migrations and start the server:

```bash
cd mysite
python manage.py migrate
python manage.py runserver
```
