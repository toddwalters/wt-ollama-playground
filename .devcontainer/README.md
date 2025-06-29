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
```bash
ollama serve
```

The service should be accessible at `http://localhost:11434`. You can verify it's running by visiting this URL in your browser or running:
```bash
curl http://localhost:11434
```

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
1. Ensure Ollama is running on the host: `ollama serve`
2. Check if port 11434 is accessible: `curl http://localhost:11434`
3. On some systems, you may need to bind Ollama to all interfaces: `OLLAMA_HOST=0.0.0.0 ollama serve`

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
