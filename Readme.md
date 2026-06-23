# Guestbook Application — Dockerized

## Overview
This is a simple Guestbook application built with Python Flask and Redis, containerized using Docker and orchestrated with Docker Compose. Users can submit messages through a web form, and messages are stored persistently in Redis.

The application was provided as starter code; this repository adds full containerization, networking, persistent storage, and security scanning.

## Architecture

```
User
  |
  v
Flask Web Application (port 5000)
  |
  v
Redis Database (port 6379)
```

Both services run in separate containers connected via a custom Docker bridge network, allowing the Flask app to reach Redis using the hostname `redis`.

## Project Structure

```
guestbook-docker-project/
├── app.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── README.md
```

## Docker Implementation

### Dockerfile
```dockerfile
FROM python:alpine

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

### docker-compose.yml
```yaml
version: "3.9"

services:
  web:
    build: .
    container_name: guestbook_web
    ports:
      - "5000:5000"
    depends_on:
      - redis
    networks:
      - guestbook_network

  redis:
    image: redis:alpine
    container_name: guestbook_redis
    volumes:
      - redis_data:/data
    networks:
      - guestbook_network

networks:
  guestbook_network:
    driver: bridge

volumes:
  redis_data:
```

## Docker Network
Both containers communicate over a custom bridge network (`guestbook_network`). The Flask app connects to Redis using the service name `redis` as the hostname, rather than an IP address, since Docker Compose handles internal DNS resolution between containers on the same network.

## Docker Volume (Persistence)
Redis data is persisted using a named Docker volume (`redis_data`), mounted to `/data` inside the Redis container. This ensures that guestbook messages survive container recreation.

**Persistence was tested by:**
1. Submitting a test message via the web form
2. Running `docker-compose down` (stopping and removing containers, but not the volume)
3. Running `docker-compose up -d` (recreating containers)
4. Confirming the test message was still present after restart

✅ Test passed — messages persisted correctly across container recreation.

## Running the Application

```bash
git clone https://github.com/ogbonnaebubechukwu28-sudo/guestbook-docker-project.git
cd guestbook-docker-project
docker-compose up -d
```

Visit the app at: `http://localhost:5000`

To stop the application:
```bash
docker-compose down
```

## Docker Scout — Vulnerability Scan

Initial scan using `python:3.10-slim` as the base image revealed **88 vulnerabilities** (1 Critical, 18 High, 18 Medium, 49 Low, 2 Unspecified), primarily from outdated OS-level packages (`openssl`, `perl`, `glibc`).

Using `docker scout recommendations`, the base image was switched to **`python:alpine`**, which Docker Scout identified as the most secure available option.

### Before (python:3.10-slim)
| Severity | Count |
|----------|-------|
| Critical | 1 |
| High | 18 |
| Medium | 18 |
| Low | 49 |
| Unspecified | 2 |
| **Total** | **88** |

### After (python:alpine)
| Severity | Count |
|----------|-------|
| Critical | 0 |
| High | 0 |
| Medium | 0 |
| Low | 0 |
| **Total** | **0** |

**Result:** Switching the base image eliminated all detected vulnerabilities while also reducing image size from 51 MB to 26 MB.

Scan command used:
```bash
docker scout cves ogbonnaebubechukwu28/guestbook-app:latest
```

## Docker Hub
The final image is available on Docker Hub:

**Image:** `ogbonnaebubechukwu28/guestbook-app:latest`
**Link:** https://hub.docker.com/r/ogbonnaebubechukwu28/guestbook-app

```bash
docker pull ogbonnaebubechukwu28/guestbook-app:latest
```

## Challenges and Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| `docker: unknown command: docker compose` | Older Docker version without the integrated `compose` plugin | Used the legacy hyphenated `docker-compose` command instead |
| `docker scout cves` failed with "unknown command" | Docker Scout CLI plugin not installed | Installed manually via the official install script |
| `permission denied while trying to connect to the docker API` | User not in the `docker` group | Added user to `docker` group with `usermod -aG docker $USER` and restarted WSL session |
| Initial scan showed 88 vulnerabilities | Base image (`python:3.10-slim`) had outdated OS packages | Switched to `python:alpine`, reducing vulnerabilities to 0 |

## Lessons Learned
- How Docker Compose manages multi-container networking automatically using service names as hostnames
- The difference between `docker-compose down` and `docker-compose down -v` and how that affects volume persistence
- How to use Docker Scout to assess image vulnerabilities and make informed base image decisions
- The trade-offs between Debian-based and Alpine-based images in terms of size and security surface

---

**Author:** Ebubechukwu Ogbonna
**GitHub Repository:** https://github.com/ogbonnaebubechukwu28-sudo/guestbook-docker-project
**Docker Hub:** https://hub.docker.com/u/ogbonnaebubechukwu28
```
