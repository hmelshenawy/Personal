# Docker Common Issues

## Container Exits Immediately

Check status and logs:

```bash
docker ps -a
docker logs container-name
docker inspect container-name
```

Common causes:

- The main process finished successfully.
- The app crashed on startup.
- Required environment variables are missing.
- The command or entrypoint is wrong.
- A dependency such as a database is unavailable.

## Cannot Connect To App In Browser

Check port mapping:

```bash
docker ps
docker port container-name
```

Common fixes:

- Use `-p host_port:container_port`.
- Make the app listen on `0.0.0.0`, not only `localhost` inside the container.
- Verify the app is actually listening on the expected container port.

Example:

```bash
docker run -p 3000:3000 app-name
```

## Service Cannot Reach Another Compose Service

Inside a Compose network, use the service name as the hostname.

Use this:

```text
postgres://user:password@db:5432/app
```

Do not use this from one container to another:

```text
postgres://user:password@localhost:5432/app
```

`localhost` means the current container, not the host and not another service.

## Database Data Disappeared

Likely causes:

- The database used container filesystem storage instead of a volume.
- `docker compose down -v` removed the named volume.
- `docker volume prune` removed an unused volume.

Use a named volume for database storage:

```yaml
services:
  db:
    image: postgres:16
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## Changes Do Not Appear In Container

For image-based changes:

```bash
docker compose up --build
```

For bind-mounted source code, check that the correct host directory is mounted into the expected container path.

For dependency changes, rebuild the image if dependencies are installed during `docker build`.

## Permission Problems With Mounted Files

Common causes:

- Host and container users have different user IDs.
- The container runs as root and creates root-owned files on the host.
- The app writes to a path that is not writable by the runtime user.

Useful checks:

```bash
docker compose exec service-name id
docker compose exec service-name ls -la /path
```

Prefer running containers as a non-root user in production.

## Image Build Is Slow

Improve layer caching by copying dependency files before source code.

```Dockerfile
COPY package*.json ./
RUN npm ci
COPY . .
```

Also use a `.dockerignore` file to avoid sending unnecessary files to the build context.

Common `.dockerignore` entries:

```text
node_modules
.git
dist
build
.env
*.log
```

## Image Is Too Large

Common fixes:

- Use a smaller base image when appropriate.
- Avoid copying development artifacts into the image.
- Remove package manager caches in the same layer that creates them.
- Use multi-stage builds for compiled applications.
- Add a `.dockerignore` file.

## Environment Variables Are Missing

Check what the container received:

```bash
docker inspect container-name
docker compose exec service-name env
```

Compose supports:

```yaml
services:
  app:
    env_file:
      - .env
    environment:
      NODE_ENV: production
```

Do not commit real secrets to `.env` files.

## Logs Are Empty

Docker captures stdout and stderr from the main container process.

Common causes:

- The app logs to a file instead of stdout/stderr.
- The process exits before logging.
- You are checking the wrong container or service.

Check:

```bash
docker ps -a
docker logs container-name
docker compose logs service-name
```

## Disk Space Is Full

Inspect Docker disk usage:

```bash
docker system df
```

Clean unused resources carefully:

```bash
docker container prune
docker image prune
docker network prune
```

Only remove volumes when you are sure the data is not needed:

```bash
docker volume prune
```

## Compose Recreates Containers Unexpectedly

Compose recreates containers when configuration, image, environment, volumes, or command settings change.

Use:

```bash
docker compose up -d
docker compose up -d --build
docker compose ps
```

If state must survive recreation, store it in a named volume or external service.
