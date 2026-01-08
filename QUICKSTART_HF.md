# FluidFlow Hugging Face Deployment - Quick Reference

## ✅ What Was Changed

Your FluidFlow application is now configured to work on Hugging Face Spaces (port 7860).

## 📋 Quick Deploy Checklist

### 1. Create Hugging Face Space
- Go to https://huggingface.co/spaces
- Click "Create new Space"
- Choose **Docker** as SDK
- Name your space (e.g., `fluidflow`)

### 2. Clone and Setup
```bash
# Clone your HF Space
git clone https://huggingface.co/spaces/YOUR_USERNAME/YOUR_SPACE_NAME
cd YOUR_SPACE_NAME

# Copy FluidFlow files
cp -r /path/to/FluidFlow/* .
```

### 3. Configure README
Copy the content from `README_HF.md` to your Space's `README.md`:
```bash
cp README_HF.md README.md
```

### 4. Set Environment Variables
In your Space settings (Variables and secrets):

**Required:**
- At least one AI API key:
  - `GEMINI_API_KEY` - Get from https://makersuite.google.com/app/apikey
  - `OPENAI_API_KEY` - Get from https://platform.openai.com/api-keys
  - `ANTHROPIC_API_KEY` - Get from https://console.anthropic.com/

**Optional:**
- `NODE_ENV=production` (recommended)
- `PORT=7860` (auto-set by HF, but can specify)
- `GITHUB_TOKEN` - For GitHub integration

### 5. Deploy
```bash
git add .
git commit -m "Deploy FluidFlow to Hugging Face"
git push
```

### 6. Wait for Build
- Monitor in Space's "Logs" tab
- Build takes ~5-10 minutes
- Access at: `https://YOUR_USERNAME-YOUR_SPACE_NAME.hf.space`

## 🧪 Test Locally First (Optional)

```bash
# Build Docker image
docker build -t fluidflow .

# Run on port 7860 (like Hugging Face)
docker run -p 7860:7860 \
  -e NODE_ENV=production \
  -e PORT=7860 \
  -e GEMINI_API_KEY=your_key_here \
  fluidflow

# Access at http://localhost:7860
```

## 📁 Key Files

| File | Purpose |
|------|---------|
| `Dockerfile` | Docker build configuration (port 7860) |
| `HUGGINGFACE_DEPLOYMENT.md` | Full deployment guide |
| `README_HF.md` | Template for HF Space README |
| `DEPLOYMENT_SUMMARY.md` | Technical summary of changes |

## 🔧 Port Configuration

| Environment | Port | Protocol |
|-------------|------|----------|
| Development | 3200 | HTTPS |
| HF Production | 7860 | HTTP |
| Custom | Set via `PORT` env var | HTTP/HTTPS |

## 🆘 Troubleshooting

**Build fails?**
- Check Logs tab in HF Space
- Verify Dockerfile syntax
- Ensure all dependencies in package.json

**App won't start?**
- Check environment variables are set
- Verify at least one AI API key is configured
- Check runtime logs

**Can't connect?**
- Wait for build to complete (~5-10 min)
- Check Space isn't sleeping (free tier)
- Verify URL: `https://YOUR_USERNAME-SPACE_NAME.hf.space`

## 📖 Need More Help?

See the full guide: `HUGGINGFACE_DEPLOYMENT.md`

## 🎉 That's It!

Your FluidFlow is ready for Hugging Face deployment!
