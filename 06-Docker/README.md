# Docker

Practical Docker notes for day-to-day development and deployment work.

## Core Concepts

- **Image**: A packaged filesystem and metadata used to create containers.
- **Container**: A running instance of an image with its own process space, filesystem layer, networking, and runtime settings.
- **Dockerfile**: Build instructions for creating an image.
- **Volume**: Persistent storage managed by Docker or mounted from the host.
- **Network**: Isolated communication layer for containers.
- **Docker Compose**: A tool for defining and running multi-container applications with a `compose.yml` or `docker-compose.yml` file.

## Common Workflow

```bash
docker build -t my-app .
docker run --name my-app -p 3000:3000 my-app
docker logs -f my-app
docker exec -it my-app sh
docker stop my-app
docker rm my-app
```

With Compose:

```bash
docker compose up --build
docker compose logs -f
docker compose exec app sh
docker compose down
```

## Images

Use small, trusted base images and pin important versions.

```Dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["npm", "start"]
```

Useful commands:

```bash
docker images
docker pull postgres:16
docker inspect image-name
docker rmi image-name
```

## Containers

Containers should be disposable. Persist only data that must survive container recreation.

```bash
docker ps
docker ps -a
docker inspect container-name
docker restart container-name
docker rm -f container-name
```

## Volumes

Use volumes for databases, uploads, cache data, and other persistent files.

```bash
docker volume ls
docker volume inspect volume-name
docker volume rm volume-name
```

Example Compose volume:

```yaml
services:
  db:
    image: postgres:16
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## Networks

Compose creates a default network where services can reach each other by service name.

```yaml
services:
  app:
    build: .
    depends_on:
      - db
  db:
    image: postgres:16
```

Inside `app`, connect to PostgreSQL using host `db`, not `localhost`.

Useful commands:

```bash
docker network ls
docker network inspect network-name
docker network prune
```

## Logs And Debugging

```bash
docker logs -f container-name
docker logs --tail 100 container-name
docker compose logs -f service-name
docker exec -it container-name sh
docker compose exec service-name sh
docker inspect container-name
docker stats
```

If a container exits immediately:

```bash
docker ps -a
docker logs container-name
docker inspect container-name
```

## Cleanup

Be careful with prune commands because they remove unused Docker resources.

```bash
docker system df
docker container prune
docker image prune
docker volume prune
docker network prune
docker system prune
docker system prune -a
```

Avoid `docker volume prune` unless you are sure no important database or app data is stored in unused volumes.

## Production Reminders

- Do not bake secrets into images.
- Use environment variables, secret managers, or orchestrator-managed secrets.
- Pin base image versions instead of relying on `latest`.
- Run containers as a non-root user when possible.
- Add health checks for critical services.
- Keep images small and rebuild them regularly for security updates.
- Use structured logs and send logs to the platform instead of storing them inside the container.
- Treat containers as immutable and disposable.
- Back up persistent volumes and databases.
- Test shutdown behavior so the app handles `SIGTERM` cleanly.
