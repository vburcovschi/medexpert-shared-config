# MedExpert Shared Configuration

Shared (public) configuration for MedExpert frontend and backend.

**⚠️ IMPORTANT: This repo contains NO SECRETS**

## Contents

- **appversion.env** - Application version (APP_VERSION)
- **docker-compose/** - Docker orchestration files (dev/prod/pre-prod)

## Secrets Management

Secrets are **NOT shared** - each repository maintains its own local secrets:

### Backend (medexpert)
`
Docker/config/db_secret.env    ← Local only (DB credentials)
Docker/config/ssl_secret.env   ← Local only (keystore password)
Docker/SSL/                    ← Local only (SSL certificates)
`

### Frontend (My_Med)
`
Docker/SSL/                    ← Local only (nginx SSL certs)
`

All secrets are in .gitignore - never committed.

## Setup

### 1. Clone both repositories with submodules

Backend:
`ash
git clone --recurse-submodules https://github.com/vburcovschi/medexpert.git
`

Frontend:
`ash
git clone --recurse-submodules https://github.com/BurcovschiAna/My_Med.git
`

### 2. Create local secrets (backend only)

`ash
cd medexpert

# Create from templates or copy existing
mkdir -p Docker/config
cat > Docker/config/db_secret.env <<'EOF'
DB_USERNAME=sa
DB_PASSWORD=<your-actual-password>
EOF

cat > Docker/config/ssl_secret.env <<'EOF'
KEYSTORE_PASSWORD=<your-actual-password>
EOF
`

### 3. Update docker-compose path references

In docker-compose files, all references point to ../../Docker/shared/ (the submodule).

### 4. Run containers

`ash
docker compose --env-file Docker/shared/appversion.env \
  -f Docker/shared/docker-compose/docker-compose-dev.yml up
`

## Submodule Updates

When appversion.env or docker-compose changes:

`ash
# In both backend and frontend
git submodule update --remote --merge
`

## Security

- ✅ Shared files: appversion.env, docker-compose files
- ❌ Secrets: DB credentials, keystore passwords, SSL certs
- 🔒 All secrets protected by .gitignore in respective repos
