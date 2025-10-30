# Deployment Guide

## Current Deployment (UPDATED 2025-10-30)

- **Project**: mythic-aloe-467602-t4
- **Frontend URL**: https://vue-multiagent-frontend-129438231958.us-central1.run.app
- **Backend URL**: https://vue-multiagent-backend-129438231958.us-central1.run.app
- **Cloud SQL Instance**: vue-multiagent-db (MySQL 8.0) - **Secured with Cloud SQL Auth Proxy** 🔒
- **Database**: vue_app
- **DB User**: root
- **Connection Method**: Unix socket via Cloud SQL Auth Proxy (production) / TCP (local development)

## Key Configuration Files
- `cloudbuild.yaml`: Defines Cloud Build steps for deployment, includes Cloud SQL instance connection
- `frontend/.env.production`: Production environment variables
- `frontend/nginx.conf`: Nginx configuration for frontend
- `backend/utils/config.py`: Backend configuration with environment variables
- `backend/models/database.py`: Database connection logic with Unix socket support

## Database Security (Implemented 2025-10-30)

### Cloud SQL Auth Proxy Configuration
- **Connection Type**: Unix socket for production, TCP for local development
- **Instance Connection Name**: `mythic-aloe-467602-t4:us-central1:vue-multiagent-db`
- **Public IP Authorization**: Cleared (no authorized networks)
- **Security Benefits**:
  - No public internet exposure
  - Encrypted IAM-authenticated connections
  - Protection against brute force attacks
  - Zero IP whitelisting configuration needed

### How It Works
1. Cloud Run service configured with `--add-cloudsql-instances` flag
2. Backend detects `INSTANCE_CONNECTION_NAME` environment variable
3. Connects via Unix socket at `/cloudsql/mythic-aloe-467602-t4:us-central1:vue-multiagent-db`
4. For local development, falls back to TCP connection using `DB_HOST`

## Environment Variables

Backend requires (Production):
- OPENAI_API_KEY
- SECRET_KEY (for JWT)
- DB_PASS
- INSTANCE_CONNECTION_NAME (for Cloud SQL Auth Proxy)
- DB_USER, DB_NAME
- FRONTEND_URL

Backend requires (Local Development):
- OPENAI_API_KEY
- SECRET_KEY (for JWT)
- DB_PASS
- DB_HOST (TCP connection)
- DB_USER, DB_NAME
- FRONTEND_URL

## Deployment Commands

### Full Deployment
```bash
gcloud builds submit --config cloudbuild.yaml
```

### Quick Service Updates
```bash
gcloud run deploy
```

### Frontend and Backend (Separate Services)
Frontend and backend are separate Cloud Run services deployed independently.

## Monitoring & Logs

### Check Logs
```bash
gcloud logging read "resource.type=cloud_run_revision" --limit=20 --project=mythic-aloe-467602-t4
```

### Service-Specific Logs
```bash
gcloud logging read "resource.type=cloud_run_revision AND resource.labels.service_name=vue-multiagent-backend" --limit=20 --project=mythic-aloe-467602-t4
```

## Testing Commands

### Registration Test
```bash
curl -X POST https://vue-multiagent-backend-129438231958.us-central1.run.app/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","email":"test@example.com","password":"Pass123","confirmPassword":"Pass123"}'
```

### Reset DB (Test Only - Remove Before Production)
```bash
curl -X DELETE https://vue-multiagent-backend-129438231958.us-central1.run.app/api/auth/reset-all-users-test-only
```

## Development Commands

### Frontend (from frontend/ directory)
```bash
npm run dev          # Start Vite dev server (port 5173)
npm run build        # Production build
npm run preview      # Preview production build
npm run lint         # ESLint
npm run format       # Prettier
npm run type-check   # TypeScript checking
```

### Backend (from backend/ directory)
```bash
python main.py                    # Start FastAPI server (port 8000)
uvicorn main:app --reload         # Alternative startup
python reset_db.py                # Reset database (dev only)
```

### Docker Development
```bash
docker-compose up                 # Start all services
docker-compose build              # Build containers
docker-compose up --build         # Build and start
```

Services:
- Frontend: http://localhost:5173
- Backend API: http://localhost:8000
- API docs: http://localhost:8000/docs
- MySQL: localhost:3306
- Redis: localhost:6379

## Production Cleanup Tasks

Before production deployment:
- Remove `/api/auth/reset-all-users-test-only` endpoint
- Complete frontend migration from Assistants API to Responses API
- Implement cost monitoring dashboard for built-in tools usage
