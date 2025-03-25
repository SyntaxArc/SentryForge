# SentryForge

[![GitHub Repository](https://img.shields.io/badge/GitHub-SentryForge-blue?logo=github)](https://github.com/SyntaxArc/SentryForge)

A streamlined, production-ready fork of [Sentry](https://sentry.io/) designed for self-hosting. Built for stability and ease of deployment, SentryForge offers a feature-complete alternative to the official [getsentry/self-hosted](https://github.com/getsentry/self-hosted) project.

## System Requirements

- Docker 20.10.0 or higher
- Docker Compose 2.0.0 or higher
- 4 CPU cores
- 8 GB RAM
- 20 GB disk space

## Quick Start

### Installation

Run the installation script with these recommended options:

```bash
./install.sh --no-report-self-hosted-issues SKIP_USER_CREATION=1
```

This command:
- Deploys all required services
- Initializes the database
- Disables telemetry reporting
- Skips interactive user setup

### Creating an Admin User

Once installation is complete, create your admin user:

```bash
docker compose run --rm web createuser
```

Follow the prompts to provide an email, password, and superuser confirmation.

### Accessing Sentry

Open your browser and visit:
```
http://localhost:9000
```

## Configuration

Key files to customize your setup:
- `sentry/config.yml` – Core Sentry settings
- `docker-compose.yml` – Container orchestration
- `redis.conf` – Redis configuration
- `clickhouse/config.xml` – ClickHouse database settings

## Connecting to Keycloak

SentryForge supports single sign-on (SSO) with Keycloak using SAML2 authentication, enabling centralized user management. This feature requires Sentry version 20.6.0 or later, assumed to be included in SentryForge.

### Setup Instructions

1. **Configure Keycloak:**
   - In the Keycloak admin console, create a new client with these settings:
     - **Client ID**: `https://sentry.example.com/saml/metadata/SLUG/` (replace `SLUG` with your Sentry organization slug)
     - **Valid Redirect URLs**: `https://sentry.example.com/*`
     - **Name ID Format**: `email`
   - Go to the **Keys** tab and set **Client signature required** to **off**.
   - Navigate to **Client Scopes**:
     - Remove the "Role list" scope.
     - Click on the client scope, add a predefined mapper, and select **X500 email**.
     - Set the **SAML Attribute Name** to `user_email`.
   - Under **Advanced**:
     - Set **Assertion Consumer Service POST Binding URL** to `https://sentry.example.com/saml/acs/SLUG/`.
     - Set **Logout Service POST Binding URL** to `https://sentry.example.com/saml/sls/SLUG/`.

2. **Configure SentryForge:**
   - Log in as an organization owner or manager, go to **Settings > Auth**, and select **SAML2**.
   - Choose "Custom SAML Provider" and configure:
     - **Metadata URL**: `https://keycloak.example.com/realms/REALM-NAME/protocol/saml/descriptor` (replace `REALM-NAME` with your Keycloak realm name)
     - **Attribute Mapping**:
       - User ID: `user_email`
       - Email: `user_email`
       - First Name: `firstName`
       - Last Name: `lastName`
   - Replace `sentry.example.com` with your Sentry instance URL (e.g., `http://localhost:9000` for local setups) and `SLUG` with your organization slug.

3. **Test and Enforce SSO:**
   - Test login with Keycloak credentials to verify the setup.
   - Optionally, enable **Require SSO** in **Settings > Auth** to enforce Keycloak authentication.

### Notes
- Update URLs with your actual domain and paths (e.g., `http://localhost:9000` for a default SentryForge install).
- The organization slug (`SLUG`) is found in Sentry under **Settings > General**.
- Inactive Keycloak users must be manually removed from Sentry, as automatic deprovisioning isn’t supported.
- For older Sentry versions, add `sentry-auth-saml2` to `sentry/requirements.txt` and reinstall (see Sentry docs).

For more details, refer to the [Sentry SAML2 Documentation](https://docs.sentry.io/organization/authentication/sso/saml2/).

## Troubleshooting

### Installation Issues

To reset and reinstall:

```bash
# Clean all volumes and start fresh
docker compose down --volumes
docker volume prune -f
./install.sh --no-report-self-hosted-issues SKIP_USER_CREATION=1
```

### Service Diagnostics

```bash
# Check running containers
docker compose ps

# View logs for a specific service
docker compose logs [service_name]

# Inspect web server logs
docker compose logs web
```

## Maintenance

### Backup

Create a full backup of SentryForge data:

```bash
./scripts/backup.sh
```

This includes database contents, configurations, and uploaded files.

### Restore

Restore from a backup file:

```bash
./scripts/restore.sh /path/to/backup
```

### Upgrading

Update to the latest version:

```bash
git pull
./install.sh --no-report-self-hosted-issues SKIP_USER_CREATION=1
```

## Contributing

We welcome contributions! Feel free to submit a Pull Request on GitHub.

## License

SentryForge is licensed under the same terms as Sentry.

---

*SentryForge is a community-driven project and is not officially affiliated with Sentry.*

