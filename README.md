# n8n-docker-caddy

This template is inspired by the official n8n self-hosting guide for DigitalOcean or Hetzner, with several modifications:

- PostgreSQL database instead of SQLite
- Integration with LangSmith
- Qdrant vector database added

See the official guides for a more detailed step by step explanation:

- [DigitalOcean tutorial](https://docs.n8n.io/hosting/server-setups/digital-ocean/)
- [Hetzner Cloud tutorial](https://docs.n8n.io/hosting/server-setups/hetzner/)

## Prerequisites

- Docker installed
- A domain name with DNS configured
- Basic understanding of Docker and networking
- Server with ports 80 and 443 available

## Quick Start

1. Clone this repository:

```bash
git clone https://github.com/jwa91/n8n-docker-caddy.git
cd n8n-docker-caddy
```

2. Create external Docker volumes:

```bash
docker volume create caddy_data
docker volume create n8n_data
docker volume create postgres_data
docker volume create qdrant_storage
```

3. Create and configure your `.env` file with the following content:

```env
DATA_FOLDER=<directory-path>/n8n-docker-caddy

DOMAIN_NAME=example.eu
SUBDOMAIN=flow

GENERIC_TIMEZONE=Europe/Amsterdam

SSL_EMAIL=<email>

POSTGRES_DB=n8n
POSTGRES_USER=your_username
POSTGRES_PASSWORD=your_password

N8N_ENCRYPTION_KEY=your-encryption-key

LANGCHAIN_API_KEY=your-langchain-api-key
```

4. Update Caddy configuration:

   - Edit `caddy_config/Caddyfile` with your domain:
     ```
     flow.your-domain.com {
         reverse_proxy n8n:5678 {
           flush_interval -1
         }
     }
     ```

5. Start the stack:

```bash
docker compose up -d
```

## Directory Structure

```
.
├── README.md
├── LICENSE
├── docker-compose.yml
├── .env
├── local_files/
│   └── .gitkeep
└── caddy_config/
    ├── Caddyfile
    └── .gitkeep
```

## Components

### Core Components

- **Caddy**: Reverse proxy with automatic HTTPS
- **n8n**: Main workflow automation platform
- **PostgreSQL**: Database for n8n (replaces default SQLite)

### AI/ML Components

- **Qdrant**: Vector database for efficient similarity search and vector storage
- **LangSmith**: Integrated for prompt monitoring and optimization

## Configuration

### Environment Variables

| Variable           | Description                         | Required | Example                 |
| ------------------ | ----------------------------------- | -------- | ----------------------- |
| DATA_FOLDER        | Local path to your n8n installation | Yes      | `/opt/n8n-docker-caddy` |
| DOMAIN_NAME        | Your domain name                    | Yes      | `example.com`           |
| SUBDOMAIN          | Subdomain for n8n                   | Yes      | `flow`                  |
| GENERIC_TIMEZONE   | Your timezone                       | Yes      | `Europe/Amsterdam`      |
| POSTGRES_DB        | PostgreSQL database name            | Yes      | `n8n`                   |
| POSTGRES_USER      | PostgreSQL username                 | Yes      | `n8n_user`              |
| POSTGRES_PASSWORD  | PostgreSQL password                 | Yes      | `your-secure-password`  |
| N8N_ENCRYPTION_KEY | Encryption key for n8n              | Yes      | `32-character-string`   |
| LANGCHAIN_API_KEY  | Your LangChain API key              | Yes      | `your-api-key`          |

## Maintenance

### Updates

To update the stack:

```bash
# Pull latest version
docker compose pull

# Stop and remove older version
docker compose down

# Start the container
docker compose up -d
```

### Backup

To backup your data, you should regularly backup these Docker volumes:

- `caddy_data`
- `n8n_data`
- `postgres_data`
- `qdrant_storage`
