<div align="center">

# ☁️ Topic 2: Cloud Deployment
![Difficulty](https://img.shields.io/badge/Difficulty-🟡_Intermediate-yellow) ![Time](https://img.shields.io/badge/Time-2_Days-blue)

</div>

---

> 🏠 [Home](../../README.md) > [Phase 6](../README.md) > **Cloud Deployment**

---

## 🎯 Why This Matters

Your AI API needs to be accessible on the internet. You'll learn to deploy on **Render** (free tier), **Railway**, and understand AWS/GCP basics.

---

## 📚 Core Concepts

### Deploying to Render (Free)

`yaml
# render.yaml
services:
  - type: web
    name: ai-api
    runtime: docker
    dockerfilePath: ./Dockerfile
    envVars:
      - key: OPENAI_API_KEY
        sync: false
      - key: DATABASE_URL
        fromDatabase:
          name: ai-db
          property: connectionString
    healthCheckPath: /health
    autoDeploy: true

databases:
  - name: ai-db
    databaseName: aidb
    plan: free
`

### Deployment Platforms Comparison

| Platform | Free Tier | Docker | Auto-deploy | Best For |
|----------|----------|--------|-------------|----------|
| **Render** | ✅ 750h/mo | ✅ | ✅ | Side projects |
| **Railway** | ✅  credit | ✅ | ✅ | Quick deploys |
| **Fly.io** | ✅ 3 VMs | ✅ | ✅ | Global edge |
| **AWS ECS** | 12mo trial | ✅ | ✅ | Enterprise |

### Docker Deploy Steps

`ash
# 1. Build production image
docker build -t ai-api:prod .

# 2. Test locally
docker run -p 8000:8000 --env-file .env ai-api:prod
curl http://localhost:8000/health

# 3. Push to registry (Render auto-builds from GitHub)
git push origin main  # Render auto-deploys
`

---

> 💡 **Pro Tip:** Start with Render's free tier. It's enough for portfolio projects and demos. Upgrade to paid only when you need always-on (free tier sleeps after 15 min of inactivity).

---

<div align="center">

[← Topic 1](../topic-1-fastapi-production/) · [📋 Phase 6](../README.md) · [Topic 3 →](../topic-3-langsmith-langfuse/)

</div>
