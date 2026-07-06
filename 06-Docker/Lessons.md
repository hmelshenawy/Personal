# Docker Lessons

## Containers Are Disposable

Do not rely on files written inside a container unless they are in a volume or bind mount. Containers can be recreated at any time.

Use containers for runtime isolation, not permanent storage.

## Images Should Be Reproducible

Prefer pinned versions over `latest` for important runtime images.

```Dockerfile
FROM node:22-alpine
```

This is usually better than:

```Dockerfile
FROM node:latest
```

Rebuild images regularly to receive security updates from base images.

## Build Cache Depends On Layer Order

Put stable layers before frequently changing layers.

Good Node.js pattern:

```Dockerfile
COPY package*.json ./
RUN npm ci
COPY . .
```

If `COPY . .` happens before `npm ci`, every source change can invalidate dependency installation.

## Use .dockerignore

The Docker build context can accidentally include large or sensitive files.

Common entries:

```text
node_modules
.git
.env
dist
build
coverage
*.log
```

This improves build speed and reduces the chance of copying secrets into images.

## localhost Changes Meaning Inside Containers

Inside a container, `localhost` means that same container.

For Compose services, connect by service name:

```text
http://api:3000
postgres://app:app@db:5432/app
```

For host access from Docker Desktop, `host.docker.internal` is often available.

## Prefer Named Volumes For Persistent Service Data

Databases should normally use named volumes.

```yaml
volumes:
  - postgres_data:/var/lib/postgresql/data
```

Bind mounts are useful for source code during local development, but named volumes are usually safer for service data.

## Logs Should Go To stdout And stderr

Container platforms expect logs from stdout and stderr.

Avoid writing only to files inside the container. File logs can disappear when containers are removed and are harder for platforms to collect.

## Health Checks Are Not A Replacement For Readiness

`depends_on` controls startup order, but it does not guarantee that a dependency is ready to accept traffic unless health checks and readiness handling are configured.

Apps should retry connections to dependencies such as databases and queues.

## Keep Dev And Production Concerns Separate

Local development often uses bind mounts, hot reload, exposed database ports, and simple credentials.

Production should use stricter settings:

- No default credentials.
- No unnecessary host port exposure.
- Non-root runtime user when possible.
- Minimal image contents.
- Proper secrets management.
- Persistent data backups.

## Clean Up Deliberately

Docker cleanup commands are useful, but volume cleanup can remove important data.

Safer first step:

```bash
docker system df
```

Then remove specific unused resources. Avoid broad prune commands when you are unsure what they will delete.

## Debug From The Container's Point Of View

When something fails, inspect the environment inside the container.

```bash
docker compose exec app sh
env
pwd
ls -la
```

Check networking from the same container that has the problem, not only from the host machine.

## Production Checklist

- Pin important image versions.
- Do not store secrets in images or committed files.
- Run as non-root where practical.
- Use health checks and graceful shutdown.
- Send logs to stdout/stderr.
- Back up persistent volumes and databases.
- Limit exposed ports.
- Rebuild images for security updates.
- Monitor CPU, memory, disk usage, and restart behavior.
