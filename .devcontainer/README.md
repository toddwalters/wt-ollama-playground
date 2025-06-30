# Dev Container Configuration

This dev container is configured for **Ollama AI/GenAI development** with Python, providing a complete environment for working with large language models and AI applications.

## 🚀 Quick Start

### Prerequisites
1. **Docker** - Must be installed and running on your host machine
2. **VS Code** with the Dev Containers extension
3. **Ollama** - Will be set up in the next steps

### Get Started in 3 Steps

1. **Install Ollama on your host machine:**
   - **macOS**: `brew install ollama`
   - **Linux**: `curl -fsSL https://ollama.ai/install.sh | sh`
   - **Windows**: Download from [ollama.ai](https://ollama.ai)

2. **Start Ollama:**
   ```bash
   ollama serve
   ```

3. **Open in Dev Container:**
   - Open this project folder in VS Code
   - Click "Reopen in Container" when prompted
   - Wait for the container to build (first time may take several minutes)

**You're ready to go!** The dev container can access Ollama via `http://host.docker.internal:11434`.

## 📋 What's Included

### Base Environment
- **OS**: Debian GNU/Linux 12 (bookworm)
- **Python**: 3.11 with pip3
- **Node.js**: Latest stable version with npm and eslint
- **Git**: Up-to-date version built from source

### Pre-installed Tools
The following command-line tools are available on the PATH:
- `apt`, `dpkg` - Package management
- `git` - Version control
- `curl`, `wget` - HTTP clients
- `ssh`, `scp`, `rsync` - Remote access and file transfer
- `gpg` - Encryption
- `ps`, `lsof`, `netstat`, `top` - System monitoring
- `tree`, `find`, `grep` - File operations
- `zip`, `unzip`, `tar`, `gzip`, `bzip2`, `xz` - Archive tools

### VS Code Extensions
- `ms-python.python` - Python language support
- `ms-toolsai.jupyter` - Jupyter notebook support
- `esbenp.prettier-vscode` - Code formatting

### Python Packages
The following packages are automatically installed via `requirements.txt`:
- **jupyterlab** - Interactive development environment
- **langchain** - LLM application framework
- **llama-index** - Data framework for LLMs
- **chromadb** - Vector database
- **sentence-transformers** - Sentence embeddings
- **openai** - OpenAI API client
- **requests** - HTTP library
- **tiktoken** - Tokenizer
- **matplotlib** - Plotting library
- **python-dotenv** - Environment variable management

## 🗂️ Component Configuration

### Dev Container Details

#### Network Configuration
- **Port Forwarding**: Port 11434 is forwarded from the container to access the host's Ollama service
- **Environment Variable**: `OLLAMA_API_BASE_URL=http://host.docker.internal:11434` connects to Ollama running on the host

#### Volume Mounts
Currently commented out in the configuration:
```json
"mounts": [
  "source=${localWorkspaceFolder}/models,target=/workspace/models,type=bind"
]
```
Uncomment this line in `devcontainer.json` if you want to mount a local models directory.

#### Verification
Once the container is running, open a terminal in VS Code and verify:

```bash
# Check Python and packages
python3 --version
pip list

# Test Ollama connection
python3 -c "import requests; print(requests.get('http://host.docker.internal:11434').text)"

# Start Jupyter Lab (optional)
jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root
```

### Ollama Server Configuration

Ollama is the LLM runtime that powers your AI development environment.

#### Basic Setup (Recommended for Dev Container)

**Installation:**
- **macOS**: `brew install ollama`
- **Linux**: `curl -fsSL https://ollama.ai/install.sh | sh`
- **Windows**: Download from [ollama.ai](https://ollama.ai)

**Start Ollama (Standard):**
```bash
ollama serve
```

This default setup works perfectly with dev containers. The dev container accesses Ollama via `http://host.docker.internal:11434`.

**Verify it's running:**
```bash
curl http://localhost:11434
```

#### Network Access Setup (Optional)

If you want to allow other devices on your network to access Ollama:

```bash
OLLAMA_HOST=0.0.0.0 ollama serve &
```

This makes Ollama accessible at:
- **Local**: `http://localhost:11434`
- **Network**: `http://YOUR_IP:11434`

**⚠️ Security Note:** This exposes Ollama to your entire network. See the Security section below for firewall configuration.

#### Download Models

```bash
# Example: Download Llama 2 7B model
ollama pull llama2

# Example: Download Code Llama model
ollama pull codellama

# List available models
ollama list
```

### Open WebUI (Web Interface)

Open WebUI provides a ChatGPT-style web interface for your Ollama models.

#### What is Open WebUI?

- **ChatGPT-Style Interface**: Familiar chat interface for interacting with LLMs
- **Model Management**: Easy switching between different models
- **Multi-User Support**: Admin controls and user management
- **Offline Operation**: Complete privacy with local data storage
- **Extensible**: Plugin support and customization options

#### Quick Setup

**1. Pull the Docker image:**
```bash
docker pull ghcr.io/open-webui/open-webui:main
```

**2. Deploy the container:**

For remote Ollama server:
```bash
docker run -d \
  -p 3000:8080 \
  -e WEBUI_AUTH=False \
  -e OLLAMA_BASE_URL=http://192.168.1.11:11434 \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

For local Ollama (with dev container):
```bash
docker run -d \
  -p 3000:8080 \
  -e WEBUI_AUTH=False \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

**3. Access the interface:**
- Open [http://localhost:3000](http://localhost:3000) in your browser

#### Configuration Options

**Environment Variables:**
- `WEBUI_AUTH=False` - Disables login (single-user mode)
- `OLLAMA_BASE_URL` - Points to your Ollama server
- `WEBUI_NAME` - Customize interface title

**Important Notes:**
- Volume mapping (`-v open-webui:/app/backend/data`) preserves your conversations and settings
- Port mapping (`-p 3000:8080`) makes the interface available on port 3000
- First account created becomes Administrator (if auth enabled)
- Cannot switch between single-user and multi-user modes after setup

#### Updating Open WebUI

```bash
# Stop and remove current container
docker rm -f open-webui

# Pull latest version
docker pull ghcr.io/open-webui/open-webui:main

# Restart with same configuration
docker run -d -p 3000:8080 -e WEBUI_AUTH=False -e OLLAMA_BASE_URL=http://host.docker.internal:11434 -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

## 🔒 Advanced Configuration

### Security (Network Access)

**⚠️ Only needed if using `OLLAMA_HOST=0.0.0.0` for network access**

#### macOS Firewall with pf (Packet Filter)

When exposing Ollama to your network, use firewall rules to control access:

**1. Create firewall rules:**
```bash
sudo nano /etc/pf.anchors/ollama.rules
```

**2. Add rules (example):**
```bash
# Block all external access to Ollama port 11434 by default
block in quick on en0 proto tcp from any to any port 11434

# Allow localhost access
pass in quick on lo0 proto tcp from 127.0.0.1 to any port 11434

# Allow Docker bridge networks
pass in quick proto tcp from 172.16.0.0/12 to any port 11434
pass in quick proto tcp from 192.168.0.0/16 to any port 11434

# Allow specific trusted IPs (uncomment and modify as needed)
# pass in quick proto tcp from 192.168.1.100 to any port 11434
```

**3. Load the rules:**
```bash
sudo pfctl -e
sudo pfctl -a ollama -f /etc/pf.anchors/ollama.rules
```

**4. Make persistent (optional):**
Edit `/etc/pf.conf` and add:
```bash
load anchor "ollama" from "/etc/pf.anchors/ollama.rules"
```
Then: `sudo pfctl -f /etc/pf.conf`

#### Simpler Alternative: macOS Application Firewall

1. **System Preferences** → Security & Privacy → Firewall
2. **Enable firewall** → Firewall Options
3. **Add Ollama** and set to "Allow incoming connections"

### Customization

#### Adding VS Code Extensions
Edit `devcontainer.json`:
```json
"extensions": [
  "ms-python.python",
  "ms-toolsai.jupyter",
  "esbenp.prettier-vscode",
  "your-new-extension-id"
]
```

#### Adding Python Packages
Add to `requirements.txt` and rebuild, or install manually:
```bash
pip install your-package-name
```

#### Environment Variables
Edit `devcontainer.json`:
```json
"remoteEnv": {
  "OLLAMA_API_BASE_URL": "http://host.docker.internal:11434",
  "YOUR_CUSTOM_VAR": "value"
}
```

## 🛠️ Troubleshooting

### Dev Container Issues

**Container Build Problems:**
1. Ensure Docker has sufficient resources allocated
2. Check internet connection (downloads packages during build)
3. Try rebuilding: Command Palette → `Dev Containers: Rebuild Container`

**Python Package Issues:**
1. Check `requirements.txt` for syntax errors
2. Try manual installation: `pip install -r requirements.txt`
3. Update pip: `pip install --upgrade pip`

### Ollama Connection Issues

**Can't connect from dev container:**

1. **Verify Ollama is running:**
   ```bash
   # Standard setup (works with dev containers)
   ollama serve
   
   # Or for network access
   OLLAMA_HOST=0.0.0.0 ollama serve &
   ```

2. **Test local access:**
   ```bash
   curl http://localhost:11434
   ```

3. **Test from dev container:**
   ```bash
   curl http://host.docker.internal:11434
   ```

4. **Check firewall/networking:**
   - Ensure firewall allows port 11434
   - On some systems, try your host machine's IP instead of `host.docker.internal`
   - Verify Docker's network configuration

### Open WebUI Issues

**"Failed to connect to Ollama":**
- Verify `OLLAMA_BASE_URL` is correct
- Ensure Ollama is accessible from the container
- Check container logs: `docker logs open-webui`

**Container won't start:**
- Check if port 3000 is in use: `netstat -tulpn | grep 3000`
- Verify Docker resources
- Check logs for specific errors

**Data not persisting:**
- Verify volume mount: `docker volume ls | grep open-webui`
- Check the volume path in your docker run command

### Getting Help

**Useful Commands:**
```bash
# Check Docker status
docker --version
docker ps

# Check Ollama
curl http://localhost:11434

# View container logs
docker logs open-webui
docker logs <container-name>

# Test network connectivity
ping host.docker.internal
```

**Community Resources:**
- [Open WebUI Discord](https://discord.gg/5rJgQTnV4s)
- [Open WebUI GitHub Issues](https://github.com/open-webui/open-webui/issues)
- [Ollama GitHub](https://github.com/ollama/ollama)

## 🎯 Next Steps

Once everything is set up:

1. **Open `notebooks/sample_query.ipynb`** to start with Ollama queries
2. **Access Open WebUI** at [http://localhost:3000](http://localhost:3000) for chat interface
3. **Download models** you want to use: `ollama pull llama2`
4. **Start developing** your AI/ML applications!

**You now have:**
- ✅ Dev container with Python, Jupyter, and AI libraries
- ✅ Ollama server for running LLMs
- ✅ Open WebUI for easy model interaction
- ✅ Complete offline AI development environment
