# Docker Compose Instructions

This project runs two services with Docker Compose:

- **Todolist Django application** — available on port `8080`
- **MySQL database** — stores application data in a persistent Docker volume

## Requirements

Make sure Docker Desktop (or Docker Engine with Docker Compose) is installed and running.

Check Docker:

```bash
docker --version
docker compose version
```

## Build and start the project

From the project directory, run:

```bash
docker compose up --build
```

This command will:

1. Build the Todolist application image.
2. Start the MySQL container.
3. Wait until MySQL becomes healthy.
4. Start the Django application container.
5. Run Django database migrations automatically.
6. Start the Django development server on port `8080`.

To run containers in the background, use:

```bash
docker compose up -d --build
```

## Open the application

Open the following URL in a browser:

```text
http://localhost:8080
```

## Check running containers

```bash
docker compose ps
```

Both the Django application and MySQL containers should be running. The MySQL service should become `healthy` after initialization.

## View logs

View logs for all services:

```bash
docker compose logs -f
```

View only Django application logs:

```bash
docker compose logs -f pythonapp
```

View only MySQL logs:

```bash
docker compose logs -f mysql
```

Press `Ctrl+C` to stop following the logs.

## Database migrations

Database migrations are executed automatically when the Django container starts.

The application container uses an entrypoint similar to:

```dockerfile
ENTRYPOINT ["sh", "-c", "python manage.py migrate && exec python manage.py runserver 0.0.0.0:8080"]
```

Therefore, it is not necessary to run `python manage.py migrate` manually after `docker compose up`.

If migrations need to be executed manually for debugging, run:

```bash
docker compose exec pythonapp python manage.py migrate
```

## Stop the containers

To stop and remove the containers and Docker network:

```bash
docker compose down
```

The MySQL data will remain stored because the project uses the persistent `mysql_data` volume.

## Start the project again

```bash
docker compose up -d
```

The existing MySQL data will be reused from the persistent volume.

## Rebuild the application

After changing the Dockerfile or dependencies, rebuild and start the project:

```bash
docker compose up -d --build
```

To rebuild without Docker cache:

```bash
docker compose build --no-cache
docker compose up -d
```

## Reset the database

To completely remove the containers **and delete the MySQL database data**, run:

```bash
docker compose down -v
```

Then start the project again:

```bash
docker compose up -d --build
```

**Warning:** `docker compose down -v` removes the persistent MySQL volume and all stored todos.

## Persistent MySQL data

MySQL data is stored in the Docker volume:

```text
mysql_data
```

The volume is mounted to:

```text
/var/lib/mysql
```

Because of this volume, todo records remain available after normal container restarts or after running:

```bash
docker compose down
```

To list Docker volumes:

```bash
docker volume ls
```

## Useful commands

```bash
# Build and start
docker compose up -d --build

# Check containers
docker compose ps

# View logs
docker compose logs -f

# Stop containers
docker compose down

# Restart containers
docker compose restart

# Rebuild without cache
docker compose build --no-cache

# Remove containers and database volume
docker compose down -v
```
