# Docker Compose Stack Network Setup

## Overview

Both PostgreSQL and Redis stacks use a common Docker network called `docker-compose-stack-network` to enable communication between services across different compose files.

## Prerequisites

Before running any of the docker-compose files, you must create the shared network.

## Setup Instructions

### 1. Create the Common Network

Run this command once before starting any services:

```bash
docker network create docker-compose-stack-network
```

### 2. Start the Services

You can now start either or both stacks:

**PostgreSQL Stack:**

```bash
docker-compose -f pgstack-compose.yml up -d
```

**Redis Stack:**

```bash
docker-compose -f redis-stack-compose.yml up -d
```

**Both Stacks:**

```bash
docker-compose -f pgstack-compose.yml -f redis-stack-compose.yml up -d
```

### 3. Verify Network

To verify that the network exists and see connected containers:

```bash
docker network inspect docker-compose-stack-network
```

## Network Benefits

- **Inter-service Communication**: Services from different compose files can communicate using container names
- **Isolated Environment**: All services are on a dedicated network separate from other Docker containers
- **Easy Service Discovery**: Use service names (e.g., `db`, `redis`, `pgadmin`) as hostnames

## Example: Connecting from Application

If you have an application container on the same network, you can connect to:

- **PostgreSQL**: `postgresql://admin:admin@365@db:5432/postgres`
- **Redis**: `redis://admin:admin@365@redis:6379`
- **PgAdmin**: `http://pgadmin:80`

## Cleanup

To remove the network (stop all containers first):

```bash
docker-compose -f pgstack-compose.yml down
docker-compose -f redis-stack-compose.yml down
docker network rm docker-compose-stack-network
```

## Troubleshooting

**Error: "network docker-compose-stack-network not found"**

- Solution: Run `docker network create docker-compose-stack-network`

**Error: "network docker-compose-stack-network already exists"**

- This is fine, the network is already created and ready to use

**Services can't communicate**

- Verify all containers are on the same network: `docker network inspect docker-compose-stack-network`
- Check container names: `docker ps`
