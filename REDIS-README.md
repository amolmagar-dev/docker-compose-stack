# Redis Stack with Built-in RedisInsight

A Docker Compose setup for running Redis Stack with its built-in RedisInsight web interface for easy Redis database management.

## 📋 Overview

This repository contains a complete Redis development environment with:

- **Redis Stack 7.4.0** - Redis with modules (RedisJSON, RedisSearch, RedisGraph, RedisTimeSeries, RedisBloom)
- **RedisInsight (Built-in)** - Web-based Redis administration and visualization tool included in Redis Stack

## 🚀 Quick Start

### Prerequisites

- Docker installed on your system
- Docker Compose installed

### Starting the Stack

1. Navigate to this directory:

```bash
cd /path/to/pgstack
```

2. Start the services:

```bash
docker-compose -f redis-stack-compose.yml up -d
```

3. Check the status:

```bash
docker-compose -f redis-stack-compose.yml ps
```

### Stopping the Stack

```bash
docker-compose -f redis-stack-compose.yml down
```

To stop and remove volumes (⚠️ this will delete your Redis data):

```bash
docker-compose -f redis-stack-compose.yml down -v
```

## 🔌 Access Information

### Redis Stack

- **Host:** `localhost`
- **Port:** `6379` (Redis)
- **Port:** `8001` (RedisInsight - built-in)
- **Username:** `admin`
- **Password:** `admin@365`

**Connection String:**

```
redis://admin:admin@365@localhost:6379
```

**Connection Command:**

```bash
redis-cli -h localhost -p 6379 --user admin -a admin@365
```

### RedisInsight (Built-in)

- **URL:** http://localhost:8001
- **Note:** RedisInsight is built into Redis Stack and automatically connected to the Redis instance

## 📝 Configuration

### Using the Built-in RedisInsight

1. Open http://localhost:8001 in your browser
2. RedisInsight is automatically connected to your Redis Stack instance
3. No additional configuration needed - you can start exploring your data immediately!

### Redis Stack Features

Redis Stack includes the following modules:

- **RedisJSON** - JSON document storage
- **RedisSearch** - Full-text search and secondary indexing
- **RedisGraph** - Graph database
- **RedisTimeSeries** - Time-series data structures
- **RedisBloom** - Probabilistic data structures (Bloom filters, Cuckoo filters)

## 📂 Data Persistence

Redis data is stored in the `./redis-data` directory on your host machine. This ensures your data persists even when containers are stopped or removed.

**Volume mapping:**

```yaml
./redis-data:/data
```

## 🔧 Service Configuration

### Redis Stack Service

- **Image:** `redis/redis-stack:7.4.0-v0`
- **Ports:**
  - 6379 (Redis)
  - 8001 (Built-in RedisInsight)
- **Restart policy:** always
- **Authentication:** Username and password protected
- **Includes:** All Redis Stack modules + RedisInsight UI

## 🛠️ Common Commands

### View Logs

```bash
# All services
docker-compose -f redis-stack-compose.yml logs

# Specific service
docker-compose -f redis-stack-compose.yml logs redis
docker-compose -f redis-stack-compose.yml logs redisinsight

# Follow logs in real-time
docker-compose -f redis-stack-compose.yml logs -f
```

### Execute Redis Commands

```bash
# Connect to Redis CLI
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365

# Execute a single command
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 INFO

# Test connection
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 PING
```

### Common Redis Operations

```bash
# Set a key
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 SET mykey "Hello Redis"

# Get a key
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 GET mykey

# List all keys
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 KEYS '*'

# Get server info
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 INFO server
```

### Backup and Restore

```bash
# Manual backup (creates dump.rdb in redis-data directory)
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 SAVE

# Or background save
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 BGSAVE

# Copy backup file
cp ./redis-data/dump.rdb ./redis-data/dump.rdb.backup
```

### Restart Services

```bash
# Restart Redis Stack (includes RedisInsight)
docker-compose -f redis-stack-compose.yml restart
```

## 🔐 Security Notes

⚠️ **Important:** The default password in this setup is for development purposes only. For production use:

1. Change the default password in the compose file
2. Use environment files (`.env`) for sensitive data
3. Don't commit credentials to version control
4. Use strong, unique passwords
5. Consider enabling TLS/SSL for Redis connections
6. Restrict network access using Docker networks
7. Use Redis ACL for fine-grained access control

## 🧪 Testing Redis Stack Modules

### RedisJSON Example

```bash
# Set JSON document
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 JSON.SET user:1 $ '{"name":"John","age":30,"city":"New York"}'

# Get JSON document
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 JSON.GET user:1
```

### RedisSearch Example

```bash
# Create index
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 FT.CREATE idx:users ON JSON PREFIX 1 user: SCHEMA $.name TEXT $.age NUMERIC

# Search
docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 FT.SEARCH idx:users "@name:John"
```

## 🐛 Troubleshooting

### Redis Container Keeps Restarting

Check the logs:

```bash
docker-compose -f redis-stack-compose.yml logs redis
```

### Cannot Connect to Redis

1. Verify the container is running:

   ```bash
   docker-compose -f redis-stack-compose.yml ps
   ```

2. Test connection:

   ```bash
   docker-compose -f redis-stack-compose.yml exec redis redis-cli --user admin -a admin@365 PING
   ```

3. Ensure port 6379 is not already in use:
   ```bash
   lsof -i :6379
   ```

### Cannot Access RedisInsight

1. Ensure Redis Stack container is running:

   ```bash
   docker-compose -f redis-stack-compose.yml ps
   ```

2. Check logs for any errors:

   ```bash
   docker-compose -f redis-stack-compose.yml logs redis
   ```

3. Ensure port 8001 is not already in use:

   ```bash
   lsof -i :8001
   ```

4. Access RedisInsight at http://localhost:8001

### Authentication Errors

If you get `(error) NOAUTH Authentication required`:

- Make sure to include the `--user admin -a admin@365` flags in redis-cli commands
- Or authenticate after connecting: `AUTH admin admin@365`

## 📚 Additional Resources

- [Redis Stack Documentation](https://redis.io/docs/stack/)
- [RedisInsight Documentation](https://redis.io/docs/connect/insight/)
- [Redis Commands Reference](https://redis.io/commands/)
- [RedisJSON Documentation](https://redis.io/docs/stack/json/)
- [RedisSearch Documentation](https://redis.io/docs/stack/search/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## 🎯 Use Cases

Redis Stack is perfect for:

- **Caching** - High-performance cache layer
- **Session Storage** - User session management
- **Real-time Analytics** - Time-series data with RedisTimeSeries
- **Full-text Search** - Document search with RedisSearch
- **JSON Documents** - Document database with RedisJSON
- **Graph Data** - Graph relationships with RedisGraph
- **Pub/Sub** - Real-time messaging
- **Rate Limiting** - API rate limiting with RedisBloom

## 📄 License

This configuration is provided as-is for development and learning purposes.

## 🤝 Contributing

Feel free to submit issues or pull requests if you have suggestions for improvements.
