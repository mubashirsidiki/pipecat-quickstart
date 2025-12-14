# Pipecat Quickstart

Build and deploy a voice AI bot in minutes.

## Prerequisites

- **Python** 3.10+
- **uv** package manager
- **Docker** and **Docker Hub** account
- **API Keys**: [Deepgram](https://console.deepgram.com/signup) | [OpenAI](https://auth.openai.com/create-account) | [Cartesia](https://play.cartesia.ai/sign-up)

## Local Development

```bash
# Setup
cp env.example .env
# Add your API keys to .env
uv sync

# Run
uv run bot.py
```

Open **http://localhost:7860** → Click `Connect`

## Deploy to Production

### 0. Install Pipecat CLI

```bash
uv tool install pipecat-ai-cli
```

### 1. Configure

Edit `pcc-deploy.toml`:
```toml
image = "YOUR_USERNAME/repo-name:0.1"  # Must include tag
```

### 2. Authenticate

```bash
docker login
uv run pipecat cloud auth login
```

### 3. Deploy

```bash
uv run pipecat cloud secrets set quickstart-secrets --file .env
uv run pipecat cloud docker build-push
```

**For Public Docker Hub Repository:**
```bash
uv run pipecat cloud deploy --no-credentials
```

**For Private Docker Hub Repository:**
1. Set up image pull secret (see [Private Docker Hub](#private-docker-hub) section below)
2. Then deploy:
```bash
uv run pipecat cloud deploy
```

### 4. Test

[Pipecat Cloud Dashboard](https://pipecat.daily.co/) → Your agent → Sandbox → Connect

## Private Docker Hub

**Only needed if your Docker Hub repository is private.**

1. Create image pull secret:
```bash
uv run pipecat cloud secrets image-pull-secret docker-hub-secret \
  https://index.docker.io/v1/ \
  YOUR_USERNAME:YOUR_ACCESS_TOKEN
```

**Example:**
```bash
uv run pipecat cloud secrets image-pull-secret docker-hub-secret \
  https://index.docker.io/v1/ \
  mubaky123:dckr_pat_************
```

> 💡 Format: `NAME HOST USERNAME:PASSWORD`. For Docker Hub, use `https://index.docker.io/v1/` as the host.

2. Add to `pcc-deploy.toml`:
```toml
image_credentials = "docker-hub-secret"
```

## Manual Docker Build

```bash
docker build --platform linux/arm64 -t USERNAME/repo:0.1 .
docker push USERNAME/repo:0.1
```

**Note**: Base image supports ARM64 only. Use `--platform linux/arm64` on AMD64 systems.

## Troubleshooting

| Issue | Fix |
|-------|-----|
| "image must include tag" | Add `:0.1` or `:latest` to image name in `pcc-deploy.toml` |
| Build fails | Use `--platform linux/arm64` flag |
| "Attempt to deploy without repository credentials" | Use `--no-credentials` for public repos, or set up `image_credentials` for private repos |
| Image pull secret command fails | Use format: `image-pull-secret NAME HOST USERNAME:PASSWORD`. For Docker Hub: `https://index.docker.io/v1/` |
| Private repo fails | Set `image_credentials` in `pcc-deploy.toml` |

## Resources

- [Documentation](https://docs.pipecat.ai/)
- [Discord Community](https://discord.gg/pipecat)

