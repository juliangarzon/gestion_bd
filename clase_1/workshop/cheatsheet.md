# Docker & Docker Compose Cheat Sheet

## Docker Basic Commands

### Container Management

**List running containers:**
```bash
docker ps
```
Shows all currently running containers with their IDs, names, and status.

**List all containers (including stopped):**
```bash
docker ps -a
```
The `-a` flag shows all containers regardless of their state.

**Run a new container:**
```bash
docker run [OPTIONS] IMAGE [COMMAND]
```
Creates and starts a new container from an image.

Common options:
- `-d` : Run in detached mode (background)
- `--name` : Assign a name to the container
- `-p` : Port mapping (host:container)
- `-e` : Set environment variables
- `-v` : Volume mapping

**Start a stopped container:**
```bash
docker start CONTAINER_NAME_OR_ID
```

**Stop a running container:**
```bash
docker stop CONTAINER_NAME_OR_ID
```
Gracefully stops a container by sending SIGTERM signal.

**Remove a container:**
```bash
docker rm CONTAINER_NAME_OR_ID
```
Removes a stopped container. Use `-f` to force remove running containers.

**Execute command in running container:**
```bash
docker exec [OPTIONS] CONTAINER_NAME_OR_ID COMMAND
```
Common usage:
```bash
docker exec -it container_name bash
```
The `-it` flags provide an interactive terminal.

### Image Management

**List images:**
```bash
docker images
```
Shows all downloaded images on your system.

**Pull an image:**
```bash
docker pull IMAGE_NAME:TAG
```
Downloads an image from Docker Hub or other registries.

**Remove an image:**
```bash
docker rmi IMAGE_NAME:TAG
```
Removes an image from your system.

**Remove unused images:**
```bash
docker image prune
```
Removes all dangling images (images without tags).

### Logs and Inspection

**View container logs:**
```bash
docker logs CONTAINER_NAME_OR_ID
```
Options:
- `-f` : Follow log output (like tail -f)
- `--tail N` : Show last N lines

**Inspect container details:**
```bash
docker inspect CONTAINER_NAME_OR_ID
```
Returns detailed information about a container in JSON format.

### System Management

**Show Docker disk usage:**
```bash
docker system df
```
Shows how much disk space Docker is using.

**Clean up unused resources:**
```bash
docker system prune
```
Removes all stopped containers, unused networks, and dangling images.
Use `-a` to also remove unused images.

## Docker Compose Commands

### Basic Operations

**Start services:**
```bash
docker-compose up
```
Options:
- `-d` : Run in detached mode
- `--build` : Build images before starting

**Stop services:**
```bash
docker-compose stop
```
Stops running containers without removing them.

**Stop and remove services:**
```bash
docker-compose down
```
Options:
- `-v` : Also remove volumes
- `--rmi all` : Also remove images

**View running services:**
```bash
docker-compose ps
```
Shows status of all services defined in docker-compose.yml.

**View logs:**
```bash
docker-compose logs [SERVICE_NAME]
```
Options:
- `-f` : Follow log output
- `--tail N` : Show last N lines

### Service Management

**Restart services:**
```bash
docker-compose restart [SERVICE_NAME]
```

**Execute command in service:**
```bash
docker-compose exec SERVICE_NAME COMMAND
```
Example:
```bash
docker-compose exec postgres psql -U myuser
```

**Scale services:**
```bash
docker-compose up --scale SERVICE_NAME=N
```
Runs N instances of the specified service.

### Build and Update

**Build or rebuild services:**
```bash
docker-compose build [SERVICE_NAME]
```

**Pull latest images:**
```bash
docker-compose pull
```
Updates all images to their latest versions.

**Recreate containers:**
```bash
docker-compose up --force-recreate
```
Forces recreation of containers even if configuration hasn't changed.

## PostgreSQL Specific Commands

**Connect to PostgreSQL in container:**
```bash
docker exec -it CONTAINER_NAME psql -U USERNAME -d DATABASE_NAME
```

**Backup PostgreSQL database:**
```bash
docker exec CONTAINER_NAME pg_dump -U USERNAME DATABASE_NAME > backup.sql
```

**Restore PostgreSQL database:**
```bash
docker exec -i CONTAINER_NAME psql -U USERNAME DATABASE_NAME < backup.sql
```

## Environment Variables

**Set environment variable in docker run:**
```bash
docker run -e KEY=VALUE image_name
```

**Use .env file with docker-compose:**
Create a `.env` file in the same directory as docker-compose.yml:
```
POSTGRES_USER=myuser
POSTGRES_PASSWORD=mypassword
```

Then reference in docker-compose.yml:
```yaml
environment:
  POSTGRES_USER: ${POSTGRES_USER}
  POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

## Volume Management

**List volumes:**
```bash
docker volume ls
```

**Create a volume:**
```bash
docker volume create VOLUME_NAME
```

**Remove a volume:**
```bash
docker volume rm VOLUME_NAME
```

**Remove all unused volumes:**
```bash
docker volume prune
```

## Network Management

**List networks:**
```bash
docker network ls
```

**Create a network:**
```bash
docker network create NETWORK_NAME
```

**Connect container to network:**
```bash
docker network connect NETWORK_NAME CONTAINER_NAME
```

## Useful Tips

1. **Check container resource usage:**
   ```bash
   docker stats
   ```

2. **Copy files between container and host:**
   ```bash
   docker cp CONTAINER:PATH HOST_PATH
   docker cp HOST_PATH CONTAINER:PATH
   ```

3. **View real-time events:**
   ```bash
   docker events
   ```

4. **Export container as tar:**
   ```bash
   docker export CONTAINER > container.tar
   ```

5. **Save image as tar:**
   ```bash
   docker save IMAGE > image.tar
   ```

6. **Load image from tar:**
   ```bash
   docker load < image.tar
   ```

## Common Troubleshooting

**Container won't start:**
- Check logs: `docker logs CONTAINER_NAME`
- Check if ports are already in use
- Verify environment variables

**Can't connect to PostgreSQL:**
- Ensure container is running: `docker ps`
- Check port mapping
- Verify credentials in environment variables

**Data lost after container restart:**
- Use volumes to persist data
- Check volume mounts in docker-compose.yml

**Permission issues:**
- On Linux, you may need to use `sudo`
- Or add your user to the docker group: `sudo usermod -aG docker $USER`