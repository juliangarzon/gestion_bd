# PostgreSQL with Docker Workshop

Welcome to the Database Management practical workshop! In this session, you'll learn how to deploy PostgreSQL using Docker and Docker Compose.

## Prerequisites
- Docker installed on your machine
- Basic command line knowledge
- Text editor (VS Code, Sublime, etc.)

## Part 1: Running PostgreSQL with Docker Command

### Step 1: Pull and Run PostgreSQL Container

Run the following command to start a PostgreSQL container:

```bash
docker run --name my-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres:latest
```

Let's break down this command:
- `docker run`: Creates and starts a new container
- `--name my-postgres`: Names your container "my-postgres"
- `-e POSTGRES_PASSWORD=mysecretpassword`: Sets the environment variable for the postgres password
- `-d`: Runs the container in detached mode (background)
- `postgres:latest`: Uses the latest PostgreSQL image

### Step 2: Verify Container is Running

Check if your container is running:

```bash
docker ps
```

You should see your PostgreSQL container in the list.

### Step 3: Test Database Connectivity

There are several ways to verify your PostgreSQL database is running and accessible:

#### Option A: Using psql client inside container

```bash
docker exec -it my-postgres psql -U postgres
```

Once connected, you can run a simple query to test:
```sql
SELECT version();
```

Type `\q` to exit the PostgreSQL prompt.

#### Option B: Test connection without entering psql

```bash
docker exec my-postgres pg_isready -U postgres
```

This will return:
- `/var/run/postgresql:5432 - accepting connections` if the database is ready

#### Option C: Run a simple query directly

```bash
docker exec my-postgres psql -U postgres -c "SELECT 'Database is working!' as status;"
```

#### Option D: Check container health and logs

Check if PostgreSQL is ready in the logs:
```bash
docker logs my-postgres | grep "database system is ready to accept connections"
```

### Step 4: Stop and Remove Container

Stop the container:
```bash
docker stop my-postgres
```

Remove the container:
```bash
docker rm my-postgres
```

Verify it's removed:
```bash
docker ps -a
```

## Part 2: Using Docker Compose

### Step 1: Create docker-compose.yml

Create a new file called `docker-compose.yml` in your project directory:

```yaml
services:
  postgres:
    image: postgres:latest
    container_name: workshop-postgres
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
```

### Understanding docker-compose.yml

- **services**: Defines the containers to run. Each service represents a container.
- **postgres**: The name of our service (you can name it anything)
- **image**: Specifies which Docker image to use from Docker Hub or other registries
- **container_name**: Custom name for the container (optional, but helps identify it)
- **environment**: Sets environment variables inside the container:
  - `POSTGRES_USER`: Database user to create
  - `POSTGRES_PASSWORD`: Password for the database user
  - `POSTGRES_DB`: Name of the default database to create
- **ports**: Maps container port to host port (format: `host:container`)
  - `"5432:5432"` means port 5432 on your machine connects to port 5432 in the container

### Docker Compose Specifications

You can find the complete Docker Compose file reference at:
https://docs.docker.com/compose/compose-file/

Note: The `version` field is now optional and considered legacy. Modern Docker Compose automatically uses the latest specification.

### Step 2: Start Services with Docker Compose

Run the following command in the directory containing your docker-compose.yml:

```bash
docker-compose up -d
```

The `-d` flag runs containers in detached mode.

### Step 3: Verify Services

Check running services:
```bash
docker-compose ps
```

View logs:
```bash
docker-compose logs
```

### Step 4: Test Database Connectivity with Docker Compose

Now let's test that the database is accessible:

#### Option A: Connect using docker-compose exec

```bash
docker-compose exec postgres psql -U myuser -d mydatabase
```

Run a test query:
```sql
SELECT current_database(), current_user, version();
```

#### Option B: Test connection status

```bash
docker-compose exec postgres pg_isready -U myuser -d mydatabase
```

#### Option C: Run query directly

```bash
docker-compose exec postgres psql -U myuser -d mydatabase -c "SELECT 'Connection successful!' as message;"
```

#### Option D: Test from host machine (requires psql client)

If you have PostgreSQL client installed on your host:
```bash
psql -h localhost -p 5432 -U myuser -d mydatabase -c "SELECT NOW();"
```
Password: `mypassword`

#### Option E: Test with environment variables

```bash
docker-compose exec postgres sh -c 'PGPASSWORD=$POSTGRES_PASSWORD psql -U $POSTGRES_USER -d $POSTGRES_DB -c "SELECT 1+1 as result;"'
```

## Part 3: Adding Volumes for Data Persistence

### Step 1: Update docker-compose.yml with Volumes

Modify your `docker-compose.yml` to include volumes:

```yaml
services:
  postgres:
    image: postgres:latest
    container_name: workshop-postgres
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Why Volumes?

Volumes ensure your data persists even when containers are stopped or removed. Without volumes, all data would be lost when the container is removed.

### Step 2: Restart Services

Stop and remove existing containers:
```bash
docker-compose down
```

Start with the new configuration:
```bash
docker-compose up -d
```

## Understanding Docker Image Versions

### Checking Available Versions

You can find available PostgreSQL versions on Docker Hub:
https://hub.docker.com/_/postgres

### Version Tags Examples:
- `postgres:latest` - Always pulls the most recent version
- `postgres:16` - Pulls latest PostgreSQL 16.x
- `postgres:16.1` - Pulls specific version 16.1
- `postgres:16-alpine` - Lighter Alpine Linux based image

### Best Practices:
- For production, always use specific version tags (e.g., `postgres:16.1`)
- For development, `latest` is acceptable but be aware of potential breaking changes
- Alpine versions are smaller but may lack some tools

### Updating Your docker-compose.yml

To use a specific version, modify the image line:
```yaml
image: postgres:16.1
```

## Cleanup

To stop and remove all containers, networks, and volumes created by docker-compose:
```bash
docker-compose down -v
```

The `-v` flag also removes volumes.

## Next Steps

Congratulations! You've learned:
- How to run PostgreSQL using Docker commands
- How to create and use Docker Compose files
- How to persist data using volumes
- How to specify image versions

For more Docker commands and explanations, check the `cheatsheet.md` file.