# Dev Container Configuration

This dev container is configured for **Ollama AI/GenAI development** with Python, providing a complete environment for working with large language models and AI applications.

## What's Included

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

## Prerequisites

### Host Requirements
1. **Docker** - Must be installed and running on the host machine
2. **VS Code** with the Dev Containers extension
3. **Ollama** - Must be running on the host machine on port 11434

### Ollama Setup
Before starting the dev container, ensure Ollama is installed and running on your host machine:

#### Installation
- **macOS**: `brew install ollama`
- **Linux**: `curl -fsSL https://ollama.ai/install.sh | sh`
- **Windows**: Download from [ollama.ai](https://ollama.ai)

#### Start Ollama Service

**Standard Setup (Dev Container Compatible):**
```bash
ollama serve
```

This default setup works perfectly with dev containers, as they can access the host's Ollama service via `http://host.docker.internal:11434`.

**For Network Access (Optional):**
If you want to allow other clients on your network to access Ollama (e.g., other computers, mobile devices, or remote development environments), start Ollama with all interfaces binding:
```bash
OLLAMA_HOST=0.0.0.0 ollama serve &
```

This runs Ollama in the background and allows connections from any network interface. The service will be accessible at `http://localhost:11434` locally and `http://YOUR_IP:11434` from other devices on your network. You can verify it's running by visiting this URL in your browser or running:
```bash
curl http://localhost:11434
```

**Security Note:** When using `OLLAMA_HOST=0.0.0.0`, Ollama will accept connections from any network interface, making it accessible to other devices on your network. For security, consider using firewall rules to restrict access (see Security Configuration section below).

#### Download Models (Optional)
To download models for use in the dev container:
```bash
# Example: Download Llama 2 7B model
ollama pull llama2

# Example: Download Code Llama model
ollama pull codellama

# List available models
ollama list
```

## Security Configuration

### macOS Firewall Configuration with pf (Packet Filter)

When running Ollama with `OLLAMA_HOST=0.0.0.0` to allow network access, it's important to restrict which devices can connect for security. On macOS, you can use the built-in packet filter (pf) to limit which IP addresses can access the Ollama API port.

#### Create pf Rules

1. **Create a pf configuration file** for Ollama restrictions:
```bash
sudo nano /etc/pf.anchors/ollama.rules
```

2. **Add rules to restrict access** (example configuration):
```bash
# Block all external access to Ollama port 11434 by default
block in quick on en0 proto tcp from any to any port 11434

# Allow localhost access
pass in quick on lo0 proto tcp from 127.0.0.1 to any port 11434

# Allow Docker bridge network (adjust IP range as needed)
pass in quick proto tcp from 172.16.0.0/12 to any port 11434
pass in quick proto tcp from 192.168.0.0/16 to any port 11434

# Allow specific trusted IP addresses (uncomment and modify as needed)
# pass in quick proto tcp from 192.168.1.100 to any port 11434
# pass in quick proto tcp from 10.0.0.0/8 to any port 11434
```

3. **Load the pf rules**:
```bash
# Enable pf if not already enabled
sudo pfctl -e

# Load the Ollama-specific rules
sudo pfctl -a ollama -f /etc/pf.anchors/ollama.rules
```

4. **Verify the rules are active**:
```bash
sudo pfctl -a ollama -s rules
```

#### Make Rules Persistent

To make the firewall rules persistent across reboots:

1. **Edit the main pf configuration**:
```bash
sudo nano /etc/pf.conf
```

2. **Add the anchor reference** (add this line near other anchor declarations):
```bash
load anchor "ollama" from "/etc/pf.anchors/ollama.rules"
```

3. **Reload pf configuration**:
```bash
sudo pfctl -f /etc/pf.conf
```

#### Managing pf Rules

**View current rules:**
```bash
sudo pfctl -a ollama -s rules
```

**Temporarily disable Ollama rules:**
```bash
sudo pfctl -a ollama -F rules
```

**Re-enable Ollama rules:**
```bash
sudo pfctl -a ollama -f /etc/pf.anchors/ollama.rules
```

**Disable pf entirely (not recommended):**
```bash
sudo pfctl -d
```

#### Alternative: Using macOS Application Firewall

For a simpler approach, you can use the macOS Application Firewall:

1. **Open System Preferences** → Security & Privacy → Firewall
2. **Enable the firewall** if not already enabled
3. **Click "Firewall Options"**
4. **Add Ollama** to the list and set it to "Allow incoming connections"
5. **Enable "Block all incoming connections"** for other applications

This approach is less granular but easier to manage for basic security needs.

## Configuration Details

### Network Configuration
- **Port Forwarding**: Port 11434 is forwarded from the container to access the host's Ollama service
- **Environment Variable**: `OLLAMA_API_BASE_URL=http://host.docker.internal:11434` connects to Ollama running on the host

### Volume Mounts
Currently commented out in the configuration:
```json
"mounts": [
  "source=${localWorkspaceFolder}/models,target=/workspace/models,type=bind"
]
```
Uncomment this line in `devcontainer.json` if you want to mount a local models directory.

## Getting Started

### 1. Prerequisites Check
Ensure Docker and Ollama are running on your host:
```bash
# Check Docker
docker --version

# Check Ollama (should return Ollama version info)
curl http://localhost:11434
```

### 2. Open in Dev Container
1. Open the project folder in VS Code
2. When prompted, click "Reopen in Container" or use Command Palette: `Dev Containers: Reopen in Container`
3. Wait for the container to build and start (first time may take several minutes)

### 3. Verify Setup
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

### 4. Start Development
- Open `notebooks/sample_query.ipynb` to get started with Ollama queries
- The environment is ready for AI/ML development with access to your host's Ollama models

## Troubleshooting

### Ollama Connection Issues
If you can't connect to Ollama from the container:

1. **Ensure Ollama is running on the host:**
   ```bash
   # Standard setup (works with dev containers)
   ollama serve
   
   # Or for network access
   OLLAMA_HOST=0.0.0.0 ollama serve &
   ```

2. **Check if port 11434 is accessible:**
   ```bash
   curl http://localhost:11434
   ```

3. **Verify Docker can reach the host:**
   From inside the dev container, test the connection:
   ```bash
   curl http://host.docker.internal:11434
   ```

4. **Check firewall settings:**
   - Ensure your firewall allows connections to port 11434
   - If using pf rules, verify they allow Docker bridge networks
   - Test with firewall temporarily disabled to isolate the issue

5. **Docker networking issues:**
   - On some systems, `host.docker.internal` may not work
   - Try using your host machine's IP address instead
   - Check Docker's network configuration

### Container Build Issues
If the container fails to build:
1. Ensure Docker has sufficient resources allocated
2. Check your internet connection (downloads packages during build)
3. Try rebuilding: Command Palette → `Dev Containers: Rebuild Container`

### Package Installation Issues
If Python packages fail to install:
1. Check the `requirements.txt` file for syntax errors
2. Try manual installation: `pip install -r requirements.txt`
3. Update pip: `pip install --upgrade pip`

## Customization

### Adding Extensions
Edit the `devcontainer.json` file to add more VS Code extensions:
```json
"extensions": [
  "ms-python.python",
  "ms-toolsai.jupyter",
  "esbenp.prettier-vscode",
  "your-new-extension-id"
]
```

### Adding Python Packages
Add packages to `requirements.txt` and rebuild the container, or install them manually:
```bash
pip install your-package-name
```

### Modifying Environment Variables
Edit the `remoteEnv` section in `devcontainer.json`:
```json
"remoteEnv": {
  "OLLAMA_API_BASE_URL": "http://host.docker.internal:11434",
  "YOUR_CUSTOM_VAR": "value"
}
```
