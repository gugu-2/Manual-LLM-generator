# Deployment Guide

Hypasia AI is designed to run locally for development and offline AI mining, but it can be deployed to a cloud VPS for team access.

## Production Backend (FastAPI)

For production, do not run Uvicorn with `--reload`. Instead, use Gunicorn with Uvicorn workers to handle multiple concurrent mining jobs.

```bash
# Install Gunicorn
pip install gunicorn

# Run with 4 worker processes
gunicorn src.hypasia.api.main:app -w 4 -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

### Dockerizing the Backend
```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . /app

RUN pip install -e .[api,js]
RUN playwright install chromium

EXPOSE 8000
CMD ["gunicorn", "src.hypasia.api.main:app", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "--bind", "0.0.0.0:8000"]
```

## Production Frontend (Vite)

The Vite development server (`npm run dev`) should not be used in production. Build the static assets and serve them via Nginx or a lightweight static server.

```bash
cd web

# Build the production assets
npm run build

# The output will be in the /web/dist directory
```

### Nginx Configuration
```nginx
server {
    listen 80;
    server_name hypasia.yourdomain.com;

    location / {
        root /path/to/hypasia-ai/web/dist;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:8000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
