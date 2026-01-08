# Hugging Face Spaces Deployment Guide

This guide explains how to deploy FluidFlow on Hugging Face Spaces using Docker.

## Prerequisites

1. A [Hugging Face](https://huggingface.co/) account
2. At least one AI API key (Gemini, OpenAI, Anthropic, etc.)

## Deployment Steps

### 1. Create a New Space

1. Go to [Hugging Face Spaces](https://huggingface.co/spaces)
2. Click **"Create new Space"**
3. Configure your Space:
   - **Space name**: Choose a name (e.g., `fluidflow`)
   - **License**: MIT
   - **Select SDK**: Choose **Docker**
   - **Space hardware**: CPU basic (free tier) or upgrade for better performance
   - **Visibility**: Public or Private

### 2. Clone Your Space Repository

```bash
git clone https://huggingface.co/spaces/YOUR_USERNAME/YOUR_SPACE_NAME
cd YOUR_SPACE_NAME
```

### 3. Copy FluidFlow Files

Copy all FluidFlow files to your Space directory:

```bash
# From FluidFlow repository root
cp -r * /path/to/YOUR_SPACE_NAME/
cp .dockerignore /path/to/YOUR_SPACE_NAME/
cp .gitignore /path/to/YOUR_SPACE_NAME/
```

### 4. Configure Environment Variables

In your Hugging Face Space settings, add the following environment variables:

**Required:**
- `PORT=7860` (Hugging Face default port - already configured in code)
- `NODE_ENV=production`
- At least one AI API key:
  - `GEMINI_API_KEY` - Your Google Gemini API key
  - `OPENAI_API_KEY` - Your OpenAI API key
  - `ANTHROPIC_API_KEY` - Your Anthropic API key
  - `OPENROUTER_API_KEY` - Your OpenRouter API key
  - `ZAI_API_KEY` - Your ZAI API key
  - `MINIMAX_API_KEY` - Your MiniMax API key

**Optional:**
- `GITHUB_TOKEN` - Your GitHub Personal Access Token (for GitHub integration)

To add environment variables:
1. Go to your Space settings
2. Navigate to **"Variables and secrets"**
3. Click **"New secret"** for each API key
4. Add the key name and value

### 5. Create README.md for Your Space

Create a `README.md` file in your Space directory with the following content:

```markdown
---
title: FluidFlow
emoji: 🌊
colorFrom: blue
colorTo: purple
sdk: docker
pinned: false
---

# FluidFlow - AI-Powered React Page Builder

Transform wireframes and sketches into functional React applications using AI.

## Features

- **Sketch to Code**: Upload wireframes/mockups and generate complete React applications
- **Multi-AI Support**: Works with Gemini, OpenAI, Claude, and more
- **Live Preview**: Real-time preview with device simulation
- **Code Editor**: Monaco-powered editor with syntax highlighting
- **GitHub Integration**: Push directly to GitHub repositories

## Usage

1. Upload a wireframe or mockup image
2. Add context or brand logo (optional)
3. Click generate to create your React app
4. Review, iterate, and export

For more information, visit the [GitHub repository](https://github.com/Akborana5/FluidFlow).
```

### 6. Push to Hugging Face

```bash
git add .
git commit -m "Initial FluidFlow deployment"
git push
```

### 7. Wait for Build

Hugging Face will automatically build your Docker image. You can monitor the build logs in your Space's **"Logs"** tab. The build process typically takes 5-10 minutes.

### 8. Access Your Application

Once the build is complete, your FluidFlow instance will be available at:
```
https://YOUR_USERNAME-YOUR_SPACE_NAME.hf.space
```

## Configuration

### Port Configuration

FluidFlow automatically detects the Hugging Face environment and uses:
- **Port 7860** in production (Hugging Face default)
- **Port 3200** in local development

The port is configured via environment variables in this priority:
1. `PORT` environment variable
2. `SERVER_PORT` environment variable
3. Default: 7860 (production) or 3200 (development)

### CORS Configuration

The application automatically allows requests from:
- Hugging Face Spaces domains (`*.hf.space`)
- Localhost (for development)

### HTTPS/SSL

- **Production (Hugging Face)**: Uses HTTP (Hugging Face handles HTTPS)
- **Development**: Uses HTTPS with self-signed certificates

## Troubleshooting

### Build Fails

1. Check the build logs in the **"Logs"** tab
2. Ensure all dependencies are properly listed in `package.json`
3. Verify Dockerfile syntax is correct

### Application Not Starting

1. Check the runtime logs in the **"Logs"** tab
2. Verify environment variables are set correctly
3. Ensure at least one AI API key is configured

### API Errors

1. Verify your API keys are valid and have sufficient credits
2. Check the API provider's status page
3. Review the application logs for detailed error messages

### Port Issues

The application should automatically use port 7860 on Hugging Face. If you encounter port issues:
1. Verify `PORT=7860` is set in environment variables
2. Check that the Dockerfile exposes port 7860
3. Ensure `NODE_ENV=production` is set

## Performance Optimization

### Hardware Upgrade

For better performance, consider upgrading your Space hardware:
- **CPU basic** (free): Suitable for light usage
- **CPU upgrade**: Better for moderate usage
- **GPU**: Recommended for heavy usage with local AI models

### Resource Limits

Hugging Face Spaces have resource limits:
- **CPU basic**: 2 vCPUs, 16GB RAM
- **CPU upgrade**: 4 vCPUs, 32GB RAM

Monitor your resource usage in the Space settings.

## Local Development

To test the Docker setup locally before deploying:

```bash
# Build the Docker image
docker build -t fluidflow .

# Run the container
docker run -p 7860:7860 \
  -e NODE_ENV=production \
  -e PORT=7860 \
  -e GEMINI_API_KEY=your_key_here \
  fluidflow

# Access at http://localhost:7860
```

## Updating Your Space

To update your deployed application:

```bash
# Pull latest changes from FluidFlow
git pull https://github.com/Akborana5/FluidFlow.git main

# Push to Hugging Face
git add .
git commit -m "Update to latest version"
git push
```

Hugging Face will automatically rebuild and redeploy your Space.

## Security Notes

1. **API Keys**: Always use Hugging Face's secret management for API keys
2. **Private Spaces**: Consider using private Spaces for sensitive projects
3. **Rate Limiting**: The application includes built-in rate limiting (100 req/15min)
4. **Input Validation**: All user inputs are validated and sanitized

## Support

For issues and questions:
- GitHub Issues: https://github.com/Akborana5/FluidFlow/issues
- Hugging Face Discussions: Use your Space's discussion tab

## License

MIT License - see [LICENSE](LICENSE) for details.
