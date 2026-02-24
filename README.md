# n8n Docker Setup

A minimal Docker Compose configuration to run **n8n** locally with secure HTTPS support via **Caddy** reverse proxy. Optimized for macOS (Apple Silicon compatible).

## 🎯 Features

- ✅ **n8n** workflow automation engine
- 🔒 **HTTPS** locally with automatic TLS certificates (Caddy internal mode)
- 🐳 **Docker Compose** orchestration
- 🔧 **Fully configurable** via `.env`
- 📊 **Persistent data** volumes for workflows & settings

## 📋 Prerequisites

- **Docker Desktop** ([Download](https://www.docker.com/products/docker-desktop))
- Internet connection (for initial image pull)

## 🚀 Quick Start

### 1. Setup Local Domain

Add `localhost.n8n` to your system hosts file:

```bash
echo "127.0.0.1 localhost.n8n" | sudo tee -a /etc/hosts
```

Verify:
```bash
grep localhost.n8n /etc/hosts
```

### 2. Configure Environment (Optional)

Edit `.env` to customize (defaults are production-ready for local dev):

```dotenv
N8N_HOST=localhost.n8n          # Domain/hostname
N8N_PORT=443                    # Internal port (HTTPS)
N8N_PROTOCOL=https              # Protocol scheme
TZ=Europe/Paris                 # Timezone
IMAGE_N8N=n8nio/n8n:latest      # n8n image version
IMAGE_CADDY=caddy:latest        # Caddy image version
```

### 3. Start Services

```bash
docker compose up -d
```

Monitor logs:
```bash
docker compose logs -f
```

Watch for this message:
```
n8n ready on ::, port 443
Editor is now accessible via: https://localhost.n8n
```

### 4. Open n8n

🌐 **https://localhost.n8n**

> ⚠️ **Browser Security Warning:** Caddy auto-generates self-signed certificates. Your browser may warn about an untrusted CA—this is normal and safe for local development. Click "Advanced" → "Continue" or your browser's equivalent.

## 🛠️ Common Commands

### Lifecycle Management

```bash
# Start services (background)
docker compose up -d

# Stop services
docker compose down

# Restart services
docker compose restart

# View real-time logs
docker compose logs -f

# View logs from specific service
docker compose logs n8n -f
docker compose logs caddy -f

# Check service status
docker compose ps
```

### Data & Maintenance

```bash
# Reset everything (⚠️ deletes all workflows & data)
docker compose down -v

# Access n8n shell
docker compose exec n8n sh

# View container resource usage
docker stats
```

---

# 🐛 Troubleshooting

## Browser Cannot Reach https://localhost.n8n

**Step 1:** Verify hosts entry
```bash
grep localhost.n8n /etc/hosts
# Should output: 127.0.0.1 localhost.n8n
```

**Step 2:** Check containers are running
```bash
docker compose ps
# Both 'n8n' and 'caddy' should show status UP
```

**Step 3:** Review logs
```bash
docker compose logs caddy --tail=50
docker compose logs n8n --tail=50
```

## "Connection Refused"

**Solution:**
```bash
# Restart services
docker compose down
docker compose up -d

# Wait 10-15 seconds for n8n to initialize
sleep 15

# Test connection
curl -k https://localhost.n8n  # -k ignores self-signed cert warning
```

## Certificate/TLS Errors

**Expected behavior:** Browser shows certificate warning on first visit.

**Why:** Caddy generates self-signed certificates locally.

**Resolution:** 
- Click "Advanced"
- Click "Continue" (or equivalent in your browser)
- This is safe for local development

## Port 443 Already in Use

**Solution:** Update `docker-compose.yml` to use a different host port:

```yaml
caddy:
  # ...
  ports:
    - "80:80"
    - "8443:443"  # Use 8443 instead of 443
```

Then access: `https://localhost.n8n:8443`

## Services Crash on Startup

**Check logs:**
```bash
docker compose logs --tail=100
```

**Common causes:**
- Port 80/443 already in use → use different ports (see above)
- Insufficient disk space → free up disk
- Corrupted volume → `docker compose down -v` and restart

## Slow Performance or Memory Issues

**Check resource usage:**
```bash
docker stats
```

**Optimize:**
- Close unused tabs/applications
- Increase Docker Desktop memory allocation (Preferences → Resources)
- Check system logs: `docker compose logs`

# 📚 Documentation & Resources

- 📖 [n8n Official Docs](https://docs.n8n.io)
- 🐳 [Docker Compose Reference](https://docs.docker.com/compose/)
- 🔐 [Caddy Documentation](https://caddyserver.com/docs/)

---

## 📄 License

n8n is licensed under the [Sustainable Use License](https://github.com/n8n-io/n8n/blob/master/LICENSE.md).  
Caddy is licensed under the [Apache License 2.0](https://github.com/caddyserver/caddy/blob/master/LICENSE).

---

**Need help?** Check the [Troubleshooting](#-troubleshooting) section above or review Docker & n8n logs before opening an issue.


