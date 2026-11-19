# Task Manager CRUD API — FastAPI + MySQL

A small beginner-friendly backend project. It exposes REST API endpoints to create, read, update, and delete tasks.

## 1. Requirements

- Python 3.10 or newer
- Docker Desktop (recommended for running MySQL locally), or a local MySQL 8 server
- Postman (optional; Swagger UI is built in)

## 2. Project structure

```text
fastapi_mysql_crud_starter/
├── app/
│   ├── __init__.py
│   ├── database.py
│   ├── main.py
│   ├── models.py
│   └── schemas.py
├── .env.example
├── .gitignore
├── docker-compose.yml
├── requirements.txt
└── README.md
```

## 3. Start MySQL

Open a terminal in this project folder and run:

```bash
docker compose up -d
```

This starts MySQL and creates a database named `crud_app` plus a development user.

Check that the container is running:

```bash
docker compose ps
```

If you already have MySQL installed, create a database and user yourself, then update `DATABASE_URL` in your environment.

## 4. Set up Python

Create a virtual environment:

**Windows (PowerShell):**
```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

**macOS / Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Copy `.env.example` to `.env` and adjust the connection string if needed.

**Windows:**
```powershell
Copy-Item .env.example .env
```

**macOS / Linux:**
```bash
cp .env.example .env
```

The app loads `.env` automatically using `python-dotenv`. Edit `DATABASE_URL` in `.env` if your local MySQL credentials differ.

Example environment variable:

```text
DATABASE_URL=mysql+pymysql://crud_user:crud_password@localhost:3306/crud_app
```

## 5. Run the API

From the project root:

```bash
uvicorn app.main:app --reload
```

Open these URLs:

- API root: http://127.0.0.1:8000/
- Interactive API docs: http://127.0.0.1:8000/docs
- Alternative docs: http://127.0.0.1:8000/redoc
- Health check: http://127.0.0.1:8000/health

The `tasks` table is created automatically when the application starts. This automatic table creation is for learning only; use database migrations (for example, Alembic) in production.

## 6. Try the CRUD operations

You can use Swagger UI at `/docs` or Postman.

### Create — POST `/tasks`

Request body:

```json
{
  "title": "Learn FastAPI",
  "description": "Build my first CRUD backend",
  "status": "pending"
}
```

Expected result: HTTP `201 Created`, with the new task and its database ID.

### Read all — GET `/tasks`

Returns a list of tasks. Optional query parameters:

```text
GET /tasks?skip=0&limit=20
```

### Read one — GET `/tasks/1`

Returns the task with ID `1`. If it does not exist, the API returns `404 Not Found`.

### Update — PATCH `/tasks/1`

Send only the fields you want to change:

```json
{
  "status": "completed"
}
```

### Delete — DELETE `/tasks/1`

Deletes task `1`. A successful delete returns HTTP `204 No Content`.

## 7. CRUD and HTTP method mapping

| Operation | HTTP method | Endpoint |
|---|---|---|
| Create | POST | `/tasks` |
| Read all | GET | `/tasks` |
| Read one | GET | `/tasks/{task_id}` |
| Update | PATCH | `/tasks/{task_id}` |
| Delete | DELETE | `/tasks/{task_id}` |

## 8. How the code is organized

- `models.py`: SQLAlchemy model; describes the MySQL `tasks` table.
- `schemas.py`: Pydantic request/response validation.
- `database.py`: database engine, session and dependency.
- `main.py`: API routes and CRUD logic.

## 9. Troubleshooting

**Can't connect to MySQL**
- Confirm Docker is running: `docker compose ps`.
- Check that port `3306` is not already used by another MySQL server.
- Confirm the username, password, database name and host in `DATABASE_URL`.
- If you change MySQL credentials after the volume has already been initialized, changing the Compose environment alone will not update the existing database user. For a disposable learning database, `docker compose down -v` removes the database volume and its data; then run `docker compose up -d` again.

**`ModuleNotFoundError`**
- Activate the virtual environment.
- Run `pip install -r requirements.txt` again.

**PowerShell won't activate the environment**
- Use Command Prompt and run `.venv\Scripts\activate.bat`, or adjust the PowerShell execution policy for your user if appropriate.

## 10. Important security notes

This is a local learning starter, not production-ready. Before deployment:
- Change all sample passwords and never commit `.env`.
- Keep database access private; do not expose MySQL publicly.
- Use HTTPS, authentication, authorization, input validation and rate limiting as appropriate.
- Configure secrets through the cloud provider's secret manager/environment settings.
- Use Alembic migrations, structured logging, backups and monitoring.
- Restrict CORS to your actual frontend origin if you add a browser frontend.

## 11. Suggested next learning steps

1. Complete each CRUD request in Swagger UI.
2. Inspect the `tasks` table in MySQL and verify each change.
3. Add a `due_date` field and migrate the schema.
4. Add filtering by task status and search by title.
5. Add user registration and login.
6. Write automated tests.
7. Deploy the API and database using AWS or Azure managed services.

## 12. AWS / Azure deployment direction

For a first cloud deployment, deploy the API to a managed application service and use a managed MySQL database:
- AWS: App Runner or Elastic Beanstalk for the API, with Amazon RDS for MySQL.
- Azure: Azure App Service for the API, with Azure Database for MySQL.

Keep the database private where possible, configure secrets securely, and allow database connections only from the API service. Cloud setup is intentionally not automated in this starter; we can do it together after the API works locally.
