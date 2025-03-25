# SentryForge

[![GitHub Repository](https://img.shields.io/badge/GitHub-SentryForge-blue?logo=github)](https://github.com/SyntaxArc/SentryForge)

A streamlined, production-ready version of [Sentry](https://sentry.io/) for self-hosting. Feature-complete and optimized for reliable deployments.

This project is a fork of [getsentry/self-hosted](https://github.com/getsentry/self-hosted) with improvements for stability and ease of deployment.

## Requirements

- Docker 20.10.0+
- Docker Compose 2.32.2+
- 4 CPU Cores
- 8 GB RAM
- 20 GB Disk Space

## Quick Start

### Installation

Run the install script with recommended flags:

```bash
./install.sh --no-report-self-hosted-issues --skip-user-creation
```

This will:
- Set up all required services
- Configure the database
- Skip telemetry reporting
- Skip interactive user creation

### Creating a User

After installation completes, create your admin user:

```bash
docker compose run --rm web createuser
```

You'll be prompted to enter an email, password, and confirm superuser status.

### Accessing Sentry

Open your browser and navigate to:
```
http://localhost:9000
```

## Configuration

Key configuration files:
- `sentry/config.yml` - Core Sentry configuration
- `docker-compose.yml` - Container orchestration
- `redis.conf` - Redis settings
- `clickhouse/config.xml` - ClickHouse database settings

## Troubleshooting

Common issues and solutions:

### Installation Problems

```bash
# Clean all volumes and start fresh
docker compose down --volumes
docker volume prune -f
./install.sh --no-report-self-hosted-issues SKIP_USER_CREATION=1
```

### Service Inspection

```bash
# Check container status
docker compose ps

# View logs for specific service
docker compose logs [service_name]

# View web server logs
docker compose logs web
```

## Maintenance

### Backup

Back up all Sentry data:

```bash
./scripts/backup.sh
```

Backups include database data, configuration, and uploaded files.

### Restore

Restore from a previous backup:

```bash
./scripts/restore.sh /path/to/backup
```

### Upgrading

For upgrading to a newer version:

```bash
git pull
./install.sh --no-report-self-hosted-issues SKIP_USER_CREATION=1
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the same terms as Sentry.

---

*SentryForge is a community project and is not officially affiliated with Sentry.*
