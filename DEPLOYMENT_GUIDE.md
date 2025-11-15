# 🚀 AI Web Browser - Complete Deployment Guide

## 📋 Overview

This guide will help you deploy the AI Web Browser (ProProxy clone) on your Synology NAS with Docker.

## 🔧 Prerequisites

- Synology NAS with DSM 7
- Docker and Docker Compose installed
- Domain: surendramedisetti.com configured
- SSH access to your NAS
- Free AI API keys (OpenRouter, Groq, DeepSeek, or Hyperbolic)

## ⚡ Quick Start (Local Development)

### 1. Clone the Repository

```bash
git clone https://github.com/louveryastrid/ai-web-browser-proxy.git
cd ai-web-browser-proxy
```

### 2. Configure Environment

```bash
cp .env.example .env
```

Edit `.env` file:

```env
# AI API Configuration
AI_PROVIDER=openrouter
OPENROUTER_API_KEY=your_key_here
GROQ_API_KEY=your_key_here
DEEPSEEK_API_KEY=your_key_here

# Backend
PORT=5000
NODE_ENV=development

# Frontend
VITE_API_URL=http://localhost:5000/api
```

### 3. Run with Docker Compose

```bash
docker-compose up -d
```

### 4. Access the Application

- Frontend: http://localhost:3000
- Backend API: http://localhost:5000

## 🏗️ Synology NAS Deployment

### Step 1: Prepare Your NAS

1. **Enable SSH** on your Synology:
   - Control Panel → Terminal & SNMP → Enable SSH service

2. **Install Docker**:
   - Package Center → Search "Docker" → Install

3. **Create Shared Folder**:
   - Control Panel → Shared Folder → Create → Name: `docker-apps`

### Step 2: Upload Project Files

**Option A: Using Git (Recommended)**

```bash
ssh admin@medisettisurendra.us3.quickconnect.to
cd /volume1/docker-apps
git clone https://github.com/louveryastrid/ai-web-browser-proxy.git
cd ai-web-browser-proxy
```

**Option B: Manual Upload**

1. Download this repo as ZIP
2. Upload via DSM File Station to `/volume1/docker-apps/`
3. Extract the files

### Step 3: Configure Environment

```bash
cd /volume1/docker-apps/ai-web-browser-proxy
cp .env.example .env
nano .env
```

Update with your settings:

```env
AI_PROVIDER=groq
GROQ_API_KEY=gsk_your_actual_key_here
PORT=5000
NODE_ENV=production
VITE_API_URL=https://ai-browser.surendramedisetti.com/api
```

### Step 4: Deploy with Docker

```bash
cd /volume1/docker-apps/ai-web-browser-proxy
docker-compose up -d
```

Check status:

```bash
docker-compose ps
docker-compose logs -f
```

### Step 5: Configure Synology Reverse Proxy

1. **Open Control Panel** → Login Portal → Advanced → Reverse Proxy

2. **Add Frontend Proxy**:
   - Description: `AI Browser UI`
   - Source:
     - Protocol: HTTPS
     - Hostname: `ai-ui.surendramedisetti.com`
     - Port: 443
   - Destination:
     - Protocol: HTTP
     - Hostname: `localhost`
     - Port: 3000

3. **Add Backend Proxy**:
   - Description: `AI Browser API`
   - Source:
     - Protocol: HTTPS
     - Hostname: `ai-browser.surendramedisetti.com`
     - Port: 443
   - Destination:
     - Protocol: HTTP
     - Hostname: `localhost`
     - Port: 5000

4. **Enable WebSocket** (for both):
   - Custom Header → Create:
     - WebSocket: Check all WebSocket options

### Step 6: Configure DNS

Add these A records to your domain registrar:

```
ai-ui.surendramedisetti.com → Your NAS IP
ai-browser.surendramedisetti.com → Your NAS IP
```

Or use Synology DDNS if you have dynamic IP.

### Step 7: Test the Deployment

1. Visit: https://ai-ui.surendramedisetti.com
2. Enter any URL (e.g., `news.ycombinator.com`)
3. Test AI chat and tools

## 🔑 Get Free AI API Keys

### OpenRouter (Recommended)
1. Visit: https://openrouter.ai
2. Sign up and get $5 free credits
3. API Key: Account → Keys

### Groq (Fast & Free)
1. Visit: https://console.groq.com
2. Sign up with GitHub
3. Generate API key

### DeepSeek
1. Visit: https://platform.deepseek.com
2. Register and verify
3. Get API key from dashboard

### Hyperbolic
1. Visit: https://hyperbolic.xyz
2. Connect wallet or sign up
3. API section → Generate key

## 🔄 Update Deployment

```bash
cd /volume1/docker-apps/ai-web-browser-proxy
git pull
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

## 🐛 Troubleshooting

### Container Won't Start

```bash
docker-compose logs backend
docker-compose logs frontend
```

### Can't Access via Domain

- Check reverse proxy settings
- Verify DNS propagation: `nslookup ai-ui.surendramedisetti.com`
- Check firewall rules on NAS

### AI Not Responding

- Verify API key in `.env`
- Check API provider status
- Review backend logs: `docker-compose logs backend`

## 📊 n8n Automation Setup

See `n8n-workflow.json` for the complete workflow.

### Import to n8n

1. Open your n8n: https://n8n.surendramedisetti.com
2. Workflows → Import from File
3. Select `n8n-workflow.json`
4. Configure webhook URL: `https://ai-browser.surendramedisetti.com/api/webhook`

## 📱 Test Commands

### Test Backend API

```bash
curl -X POST https://ai-browser.surendramedisetti.com/api/browser/scrape \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com"}'
```

### Test AI Chat

```bash
curl -X POST https://ai-browser.surendramedisetti.com/api/ai/chat \
  -H "Content-Type: application/json" \
  -d '{"url":"https://news.ycombinator.com","message":"Summarize this page"}'
```

## 🎯 Features Checklist

- [ ] URL loading in iframe
- [ ] AI chat with webpage
- [ ] Page summarization
- [ ] Email extraction
- [ ] Job details extraction  
- [ ] LinkedIn contacts extraction
- [ ] HTML cleaning
- [ ] Convert to text
- [ ] Browsing history
- [ ] Screenshot capture
- [ ] n8n webhook integration
- [ ] Google Sheets export
- [ ] Telegram notifications

## 📦 Manual Installation (Without Docker)

See `MANUAL_INSTALL.md` for non-Docker setup instructions.

## 🌟 Next Steps

1. Customize UI in `frontend/src/`
2. Add more AI providers in `backend/src/services/ai.service.js`
3. Create n8n workflows for automation
4. Set up backup for SQLite database

## 💡 Tips

- Use Groq for fastest responses
- OpenRouter for best model selection
- Monitor logs: `docker-compose logs -f`
- Backup database: `/backend/data/browser.db`

## 📞 Support

Issues: https://github.com/louveryastrid/ai-web-browser-proxy/issues

---

✅ **You're all set! Enjoy your self-hosted AI Web Browser!**
