# Docker & Docker Compose Setup for SelfStream

## Quick Start

### Using Docker Compose (Recommended)

1. **Setup environment variables:**

   ```bash
   cp .env.example .env
   # Edit .env and add your TMDB_API_KEY
   nano .env
   ```

2. **Start the addon:**

   ```bash
   docker-compose up -d
   ```

3. **Access the addon:**
   - Open `http://localhost:7020/manifest.json` in your browser
   - Add to Stremio: Install Stremio and go to Add-ons → Addon Management → Install from URL → Paste the URL

### Using Docker CLI

1. **Build the image:**

   ```bash
   docker build -t selfstream:latest .
   ```

2. **Run the container:**
   ```bash
   docker run -d \
     --name selfstream \
     -p 7020:7020 \
     -e TMDB_API_KEY=YOUR_API_KEY \
     selfstream:latest
   ```

## Development

For development with live reload, use the host machine:

```bash
npm install
npm run dev
```

## Production Deployment

Use the production compose file with resource limits and logging:

```bash
docker-compose -f docker-compose.prod.yml up -d
```

### Features in Production Config:

- **Auto-restart**: Always restart on failure
- **Resource limits**: CPU 1 core, Memory 512MB max
- **Health checks**: Monitors `/manifest.json` endpoint
- **Logging**: Rotated JSON logs (10MB per file, max 3 files)
- **Networking**: Isolated bridge network

## Container Management

### View logs:

```bash
docker-compose logs -f selfstream
```

### Stop the addon:

```bash
docker-compose down
```

### Rebuild and restart:

```bash
docker-compose up -d --build
```

## File Structure

- **Dockerfile** - Multi-stage build (optimized for production)
- **docker-compose.yml** - Development configuration
- **docker-compose.prod.yml** - Production configuration with resource limits
- **.dockerignore** - Files excluded from Docker build
- **.env.example** - Template for environment variables

## Requirements

- Docker >= 20.10
- Docker Compose >= 2.0

## Troubleshooting

### Container won't start

```bash
docker-compose logs selfstream
```

### Port already in use

Change port in docker-compose.yml:

```yaml
ports:
  - "8020:7020" # Host port:Container port
```

### TMDB API Key issues

Verify your key is set:

```bash
docker-compose exec selfstream node -e "console.log(process.env.TMDB_API_KEY)"
```

## Performance Notes

- **Memory Usage**: ~200-300MB typical
- **CPU Usage**: Minimal, spikes during stream proxying
- **Cache**: In-memory cache, data lost on container restart
- **Network**: All streams proxied through container

## Security Considerations

- Change default TMDB_API_KEY before deployment
- Use firewall rules to restrict access if needed
- Keep base image updated: `docker pull node:20-alpine`
- Run with `restart: unless-stopped` for high availability
