<div align="center">

<h1>🐳 n8n-skillify</h1>

<p><strong>Self-hosted n8n automation container for the Skillify post-test pipeline.</strong></p>

<p>
  <img src="https://img.shields.io/badge/n8n-Automation-EF5E0E?style=flat-square&logo=n8n" />
  <img src="https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker" />
  <img src="https://img.shields.io/badge/Neon-Postgres-00E5BF?style=flat-square&logo=neon" />
</p>

</div>

---

> [!NOTE]
> This repo is part of the **[Skillify](https://github.com/i-piiyush/skillify)** project.
> It provides the n8n Docker setup that powers the post-test automation pipeline — detecting weak topics and fetching the best learning resources from the internet.

---

## 🔁 What does this do?

After a user completes the adaptive skill test on Skillify, this n8n instance runs an automated workflow:

```
Test Complete
     │
     ▼
Weak Topic Detection
     │
     ▼
Internet Resource Search (per weak topic)
     │
     ▼
Personalized Resource Report (video / article)
```

---

## ⚙️ Setup

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) & Docker Compose installed
- A [Neon](https://neon.tech) database (same one used by Skillify, or a separate one)

---

### 1. Clone this repository

```bash
git clone https://github.com/i-piiyush/n8n-skillify.git
cd n8n-skillify
```

---

### 2. Configure environment variables

Copy the example file and fill in your values:

```bash
cp .env.example .env
```

Then edit `.env`:

```env
# Neon Database
DB_POSTGRESDB_DATABASE=neondb
DB_POSTGRESDB_HOST=             # your Neon host (e.g. ep-xxx.us-east-2.aws.neon.tech)
DB_POSTGRESDB_PASSWORD=         # your Neon database password
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_SSL_ENABLED=true
DB_POSTGRESDB_SSL_REJECT_UNAUTHORIZED=false
DB_POSTGRESDB_USER=neondb_owner
DB_TYPE=postgresdb

# n8n Encryption Key (generate a random string)
N8N_ENCRYPTION_KEY=             # e.g. openssl rand -hex 32

# n8n Basic Auth (to protect your n8n dashboard)
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=            # choose a username
N8N_BASIC_AUTH_PASSWORD=        # choose a strong password

# Execution settings (optimized for low-resource environments)
EXECUTIONS_DATA_PRUNE=true
EXECUTIONS_DATA_MAX_AGE=72
EXECUTIONS_DATA_SAVE_ON_SUCCESS=none
EXECUTIONS_DATA_SAVE_ON_ERROR=all

N8N_CONCURRENCY_PRODUCTION_LIMIT=1
```

> [!TIP]
> Generate a secure encryption key with:
> ```bash
> openssl rand -hex 32
> ```

---

### 3. Start the container

```bash
docker compose up -d
```

n8n will be available at **`http://localhost:5678`**

Log in using the `N8N_BASIC_AUTH_USER` and `N8N_BASIC_AUTH_PASSWORD` you set above.

---

### 4. Import the Skillify workflow

Download the workflow file:

👉 **[Download workflow.json](https://drive.google.com/file/d/1qj2QPxIV3FtMwEgpETbsL4iMO9bxmsP2/view?usp=sharing)**

Then import it into n8n:

1. Open **`http://localhost:5678`**
2. Go to **Workflows** → **Import from file**
3. Select the downloaded `workflow.json`
4. Click **Save** and then **Activate** the workflow

---

### 5. Connect to Skillify

In your main Skillify app's `.env.local`, set:

```env
NEXT_PUBLIC_N8N_URL=http://localhost:5678
N8N_WEBHOOK_SECRET=             # must match the webhook secret configured in the n8n workflow
```

---

## 🔐 Environment Variables Reference

| Variable | Description | Required |
|---|---|---|
| `DB_POSTGRESDB_HOST` | Neon database host URL | ✅ |
| `DB_POSTGRESDB_PASSWORD` | Neon database password | ✅ |
| `DB_POSTGRESDB_DATABASE` | Database name (default: `neondb`) | ✅ |
| `DB_POSTGRESDB_USER` | Database user (default: `neondb_owner`) | ✅ |
| `DB_POSTGRESDB_PORT` | Database port (default: `5432`) | ✅ |
| `DB_POSTGRESDB_SSL_ENABLED` | Enable SSL for Neon connection | ✅ |
| `DB_POSTGRESDB_SSL_REJECT_UNAUTHORIZED` | Set to `false` for Neon compatibility | ✅ |
| `DB_TYPE` | Database type — must be `postgresdb` | ✅ |
| `N8N_ENCRYPTION_KEY` | Random secret for encrypting n8n credentials | ✅ |
| `N8N_BASIC_AUTH_ACTIVE` | Enable basic auth on the n8n dashboard | ✅ |
| `N8N_BASIC_AUTH_USER` | Username for n8n dashboard login | ✅ |
| `N8N_BASIC_AUTH_PASSWORD` | Password for n8n dashboard login | ✅ |
| `EXECUTIONS_DATA_PRUNE` | Auto-delete old execution logs | Optional |
| `EXECUTIONS_DATA_MAX_AGE` | Max age (hours) to keep execution logs | Optional |
| `EXECUTIONS_DATA_SAVE_ON_SUCCESS` | Save logs on success (`none` saves storage) | Optional |
| `EXECUTIONS_DATA_SAVE_ON_ERROR` | Save logs on error (`all` for debugging) | Optional |
| `N8N_CONCURRENCY_PRODUCTION_LIMIT` | Max concurrent workflow executions | Optional |

---

## 🛑 Stopping the container

```bash
docker compose down
```

To stop and remove all data volumes:

```bash
docker compose down -v
```

---

## 🔗 Related

- **Skillify App** → [github.com/i-piiyush/skillify](https://github.com/i-piiyush/skillify)
- **Workflow file** → [Download workflow.json](https://drive.google.com/file/d/1qj2QPxIV3FtMwEgpETbsL4iMO9bxmsP2/view?usp=sharing)
- **n8n Docs** → [docs.n8n.io](https://docs.n8n.io)

---

<div align="center">
  <p>Part of the <strong>Skillify</strong> project · Powered by n8n & Neon</p>
</div>