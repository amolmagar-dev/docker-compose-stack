# PostgreSQL Stack with pgAdmin

A Docker Compose setup for running PostgreSQL database with pgAdmin web interface for easy database management.

## 📋 Overview

This repository contains a complete PostgreSQL development environment with:

- **PostgreSQL 18** - Latest PostgreSQL database server
- **pgAdmin 4** - Web-based database administration tool

## 🚀 Quick Start

### Prerequisites

- Docker installed on your system
- Docker Compose installed

### Starting the Stack

1. Clone or navigate to this repository:

```bash
cd /path/to/pgstack
```

2. Start the services:

```bash
docker-compose up -d
```

3. Check the status:

```bash
docker-compose ps
```

### Stopping the Stack

```bash
docker-compose down
```

To stop and remove volumes (⚠️ this will delete your database data):

```bash
docker-compose down -v
```

## 🔌 Access Information

### PostgreSQL Database

- **Host:** `localhost`
- **Port:** `5432`
- **Database:** `postgres`
- **Username:** `admin`
- **Password:** `admin@365`

**Connection String:**

```
postgresql://admin:admin@365@localhost:5432/postgres
```

### pgAdmin Web Interface

- **URL:** http://localhost:5431
- **Email:** `admin@example.com`
- **Password:** `admin@365`

## 📝 Configuration

### Adding PostgreSQL Server in pgAdmin

1. Open http://localhost:5431 in your browser
2. Login with the credentials above
3. Right-click **"Servers"** → **"Register"** → **"Server"**
4. In the **General** tab:
   - Name: `Local PostgreSQL` (or any name you prefer)
5. In the **Connection** tab:
   - Host: `db` (the Docker service name)
   - Port: `5432`
   - Maintenance database: `postgres`
   - Username: `admin`
   - Password: `admin@365`
   - Save password: ✓ (optional)
6. Click **Save**

## 📂 Data Persistence

PostgreSQL data is stored in the `./data` directory on your host machine. This ensures your data persists even when containers are stopped or removed.

**Volume mapping:**

```yaml
./data:/var/lib/postgresql
```

## 🔧 Service Configuration

### PostgreSQL Service

- **Image:** `postgres:latest` (PostgreSQL 18.0)
- **Port:** 5432 (host) → 5432 (container)
- **Restart policy:** always

### pgAdmin Service

- **Image:** `dpage/pgadmin4`
- **Port:** 5431 (host) → 80 (container)
- **Restart policy:** always
- **Depends on:** PostgreSQL service

## 🛠️ Common Commands

### View Logs

```bash
# All services
docker-compose logs

# Specific service
docker-compose logs db
docker-compose logs pgadmin

# Follow logs in real-time
docker-compose logs -f
```

### Execute SQL Commands

```bash
# Connect to PostgreSQL CLI
docker-compose exec db psql -U admin -d postgres

# Execute a single command
docker-compose exec -T db psql -U admin -d postgres -c "SELECT version();"
```

### Backup Database

```bash
# Backup to file
docker-compose exec -T db pg_dump -U admin postgres > backup.sql

# Restore from file
cat backup.sql | docker-compose exec -T db psql -U admin postgres
```

### Restart Services

```bash
# Restart all services
docker-compose restart

# Restart specific service
docker-compose restart db
docker-compose restart pgadmin
```

## 🔐 Security Notes

⚠️ **Important:** The default credentials in this setup are for development purposes only. For production use:

1. Change all default passwords
2. Use environment files (`.env`) for sensitive data
3. Don't commit credentials to version control
4. Use strong, unique passwords
5. Consider using Docker secrets for production environments

## 🐛 Troubleshooting

### PostgreSQL Container Keeps Restarting

If you upgraded from an older PostgreSQL version, you may need to clear old data:

```bash
docker-compose down
rm -rf ./data
docker-compose up -d
```

### Cannot Connect to PostgreSQL

1. Verify the container is running:

   ```bash
   docker-compose ps
   ```

2. Check PostgreSQL logs:

   ```bash
   docker-compose logs db
   ```

3. Ensure port 5432 is not already in use:
   ```bash
   lsof -i :5432
   ```

### Cannot Access pgAdmin

1. Check if pgAdmin is running:

   ```bash
   docker-compose ps pgadmin
   ```

2. View pgAdmin logs:

   ```bash
   docker-compose logs pgadmin
   ```

3. Ensure port 5431 is not already in use:
   ```bash
   lsof -i :5431
   ```

## 📚 Additional Resources

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [pgAdmin Documentation](https://www.pgadmin.org/docs/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## 📄 License

This configuration is provided as-is for development and learning purposes.

## 🤝 Contributing

Feel free to submit issues or pull requests if you have suggestions for improvements.
