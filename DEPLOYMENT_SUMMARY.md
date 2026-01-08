# Hugging Face Docker Deployment - Summary of Changes

## Problem Statement
The FluidFlow application needed to be configured to work on Hugging Face Docker, which requires applications to run on port 7860.

## Changes Made

### 1. Dockerfile Updates
- Changed exposed port from 3200 to 7860 (Hugging Face's standard port)
- Updated health check to dynamically use the PORT environment variable
- Changed CMD to use `npm start` instead of `npm run server`
- Removed `--only=production` flag from npm ci to include devDependencies (needed for tsx)

### 2. Server Configuration (server/index.ts)
- **Port Configuration**: Modified port logic to prioritize environment variables:
  ```javascript
  const PORT = process.env.PORT || process.env.SERVER_PORT || (process.env.NODE_ENV === 'production' ? 7860 : 3200);
  ```
  - PORT (highest priority) - Used by Hugging Face
  - SERVER_PORT - Fallback for custom configurations
  - Default: 7860 in production, 3200 in development

- **CORS Updates**: Added Hugging Face Spaces domain patterns:
  - `*.hf.space` - Standard Hugging Face Spaces domains
  - `*-[a-z0-9-]+.hf.space` - Custom subdomain Spaces

- **Production Static File Serving**: Added middleware to serve built frontend from dist directory in production
  - Serves static files from `/app/dist`
  - Implements SPA routing (all non-API routes serve index.html)

- **Protocol Selection**: 
  - **Production**: Uses HTTP (Hugging Face handles HTTPS termination)
  - **Development**: Uses HTTPS with self-signed certificates

### 3. Package.json Updates
- Added `start` script: `NODE_ENV=production npm run server`
- This ensures production mode is enabled when using npm start

### 4. Server Watchdog Updates (server/watchdog.ts)
- Updated port logic to match server/index.ts
- Protocol detection for health checks

### 5. Documentation

#### HUGGINGFACE_DEPLOYMENT.md
Comprehensive guide covering:
- Prerequisites and setup
- Step-by-step deployment instructions
- Environment variable configuration
- Port configuration details
- CORS and HTTPS/SSL handling
- Troubleshooting common issues
- Performance optimization tips
- Local Docker testing commands
- Update procedures

#### README_HF.md
Template README for Hugging Face Space with:
- YAML frontmatter for Space configuration
- Feature highlights
- Supported AI providers
- Usage instructions
- Configuration guidance
- Links to full documentation

#### README.md Updates
- Added Docker deployment section
- Included local and production Docker run commands
- Added link to Hugging Face deployment guide

## Port Configuration Summary

| Environment | Port | Protocol | Notes |
|-------------|------|----------|-------|
| Development (local) | 3200 | HTTPS | Self-signed certificates |
| Production (local Docker) | 7860 | HTTP | Via PORT env var |
| Production (Hugging Face) | 7860 | HTTP | Hugging Face handles HTTPS |
| Custom | Any | HTTP/HTTPS | Via PORT or SERVER_PORT env vars |

## Environment Variables

For Hugging Face deployment, set these in Space settings:
- `PORT=7860` (optional - automatically set by HF)
- `NODE_ENV=production` (recommended)
- At least one AI API key:
  - `GEMINI_API_KEY`
  - `OPENAI_API_KEY`
  - `ANTHROPIC_API_KEY`
  - `OPENROUTER_API_KEY`
  - `ZAI_API_KEY`
  - `MINIMAX_API_KEY`
- `GITHUB_TOKEN` (optional - for GitHub integration)

## How to Deploy to Hugging Face

1. Create a new Docker Space on Hugging Face
2. Clone your Space repository
3. Copy FluidFlow files to the Space directory
4. Add environment variables in Space settings
5. Push to Hugging Face
6. Wait for build (5-10 minutes)
7. Access your app at `https://YOUR_USERNAME-YOUR_SPACE_NAME.hf.space`

See HUGGINGFACE_DEPLOYMENT.md for detailed instructions.

## Testing Locally

### Development Mode
```bash
npm run dev
# Frontend: https://localhost:3100
# Backend: https://localhost:3200
```

### Production Mode with Docker
```bash
docker build -t fluidflow .

docker run -p 7860:7860 \
  -e NODE_ENV=production \
  -e PORT=7860 \
  -e GEMINI_API_KEY=your_key_here \
  fluidflow

# Access at http://localhost:7860
```

## Backward Compatibility

All changes are backward compatible:
- Development mode unchanged (port 3200, HTTPS)
- Custom port configuration still works via SERVER_PORT
- Existing deployment configurations continue to function

## Architecture Notes

The application now supports two deployment modes:

1. **Development Mode** (npm run dev):
   - Frontend (Vite): Port 3100, HTTPS
   - Backend (Express): Port 3200, HTTPS
   - Vite proxies /api requests to backend

2. **Production Mode** (npm start or Docker):
   - Single server on port 7860 (or PORT env var)
   - Serves static frontend from /dist
   - API routes on /api/*
   - All other routes serve index.html (SPA routing)
   - HTTP protocol (HTTPS handled by reverse proxy)

## Files Modified

1. `Dockerfile` - Port, health check, and build configuration
2. `server/index.ts` - Port logic, CORS, static file serving, protocol selection
3. `server/watchdog.ts` - Port logic synchronization
4. `package.json` - Added start script
5. `README.md` - Added Docker deployment section
6. `HUGGINGFACE_DEPLOYMENT.md` - New comprehensive deployment guide
7. `README_HF.md` - New Hugging Face Space README template

## Next Steps

To deploy to Hugging Face:
1. Follow the guide in HUGGINGFACE_DEPLOYMENT.md
2. Test locally with Docker first (optional but recommended)
3. Configure your Space with the README_HF.md content
4. Set environment variables in Space settings
5. Push and deploy!
