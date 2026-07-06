# Docker Cheatsheet

## Daily

| Command | Hint |
|---------|------|
| `docker ps` | Running containers |
| `docker ps -a` | All containers |
| `docker images` | Local images |
| `docker logs -f app` | Follow logs |
| `docker exec -it app bash` | Enter container |
| `docker compose up -d` | Start project |
| `docker compose down` | Stop project |

---

## Build

| Command | Hint |
|---------|------|
| `docker build -t app .` | Build image |
| `docker compose build` | Build compose services |
| `docker pull nginx` | Pull image |

---

## Cleanup

| Command | Hint |
|---------|------|
| `docker image prune` | Remove dangling images |
| `docker container prune` | Remove stopped containers |
| `docker volume prune` | Remove unused volumes |
| `docker system prune` | General cleanup |

---

## Debug

| Command | Hint |
|---------|------|
| `docker logs app` | Container logs |
| `docker stats` | Resource usage |
| `docker inspect app` | Container details |
| `docker top app` | Running processes |

---

## Compose

| Command | Hint |
|---------|------|
| `docker compose up -d` | Start |
| `docker compose down` | Stop |
| `docker compose logs -f` | Logs |
| `docker compose exec app bash` | Enter service |
| `docker compose restart app` | Restart service |

---

## Flags

| Flag | Meaning |
|------|---------|
| `-d` | Detached |
| `-it` | Interactive |
| `--rm` | Remove after exit |
| `-p` | Port mapping |
| `-v` | Volume |