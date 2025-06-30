# Ollama Development Playground

A comprehensive development environment for experimenting with Ollama, large language models (LLMs), and AI applications. This repository provides a complete containerized setup with Jupyter notebooks, Python libraries, and web interfaces for AI development.

## 🚀 Quick Start

### Prerequisites
- **Docker** installed and running
- **VS Code** with Dev Containers extension
- **Ollama** (will be set up during installation)

### Get Running in 3 Steps

1. **Clone and open the repository:**
   ```bash
   git clone <repository-url>
   cd wt-ollama-playground
   code .
   ```

2. **Install Ollama on your host machine:**
   - **macOS**: `brew install ollama`
   - **Linux**: `curl -fsSL https://ollama.ai/install.sh | sh`
   - **Windows**: Download from [ollama.ai](https://ollama.ai)

3. **Start Ollama and open in dev container:**
   ```bash
   # Start Ollama
   ollama serve
   
   # In VS Code: "Reopen in Container" when prompted
   ```

You're ready to start experimenting with AI models!

## 📁 Repository Structure

```
wt-ollama-playground/
├── .devcontainer/           # Dev container configuration
│   ├── devcontainer.json    # Container setup
│   └── README.md            # Detailed setup instructions
├── .env                     # Environment variables
├── models/                  # Model configurations
│   └── llama3/             # Llama3 customizations
│       ├── Modelfile        # Custom model configuration
│       ├── notebooks/       # Model-specific experiments
│       ├── prompts/         # Custom prompt templates
│       └── notes.md         # Fine-tuning documentation
├── notebooks/               # Jupyter notebooks
│   └── sample_query.ipynb   # Getting started notebook
├── requirements.txt         # Python dependencies
└── README.md               # This file
```

## 🛠️ What's Included

### Development Environment
- **Python 3.11** with AI/ML libraries (LangChain, LlamaIndex, ChromaDB, etc.)
- **Jupyter Lab** for interactive development
- **VS Code extensions** for Python and Jupyter
- **Pre-configured networking** to connect to Ollama

### AI Libraries & Tools
- **LangChain** - Framework for LLM applications
- **LlamaIndex** - Data framework for LLMs
- **ChromaDB** - Vector database for embeddings
- **Sentence Transformers** - Text embeddings
- **OpenAI Client** - Compatible API client
- **Matplotlib** - Visualization and plotting

### Model Management
- **Custom Modelfiles** for fine-tuning
- **Prompt templates** and examples
- **Experiment tracking** and documentation
- **Version control** for model configurations

## 🎯 Usage Examples

### 1. Basic Ollama Interaction

```python
import requests
import os

# Use environment variables from .env
base_url = os.getenv('OLLAMA_API_BASE_URL')
model = os.getenv('OLLAMA_DEFAULT_MODEL')

# Simple query
response = requests.post(f"{base_url}/api/generate", json={
    "model": model,
    "prompt": "Explain quantum computing in simple terms",
    "stream": False
})

print(response.json()['response'])
```

### 2. Using LangChain

```python
from langchain_community.llms import Ollama
from langchain.prompts import PromptTemplate

# Initialize Ollama LLM
llm = Ollama(
    base_url=os.getenv('OLLAMA_API_BASE_URL'),
    model=os.getenv('OLLAMA_DEFAULT_MODEL')
)

# Create a prompt template
prompt = PromptTemplate(
    input_variables=["topic"],
    template="Write a brief explanation about {topic} for beginners."
)

# Chain the prompt and LLM
chain = prompt | llm
result = chain.invoke({"topic": "machine learning"})
print(result)
```

### 3. Working with Embeddings

```python
from sentence_transformers import SentenceTransformer
import chromadb

# Initialize embedding model
model = SentenceTransformer('all-MiniLM-L6-v2')

# Create embeddings
texts = ["Hello world", "AI is fascinating", "Machine learning rocks"]
embeddings = model.encode(texts)

# Store in ChromaDB
client = chromadb.Client()
collection = client.create_collection("my_documents")
collection.add(
    embeddings=embeddings.tolist(),
    documents=texts,
    ids=[f"doc_{i}" for i in range(len(texts))]
)
```

## 🔧 Configuration

### Environment Variables (.env)
```bash
OLLAMA_API_BASE_URL=http://host.docker.internal:11434
OLLAMA_DEFAULT_MODEL=llama3-instruct
OLLAMA_BASE_MODEL=llama3
WORKSPACE_MODELS_PATH=/workspace/models
```

### Custom Models

Create custom model variants by editing `models/llama3/Modelfile`:

```dockerfile
FROM llama3

# Custom system prompt
SYSTEM """You are a helpful AI assistant specialized in Python development."""

# Parameter tuning
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER num_ctx 4096
```

Build the custom model:
```bash
ollama create my-python-assistant -f ./models/llama3/Modelfile
```

## 📚 Available Interfaces

### 1. Jupyter Lab (Programmatic)
- **Access**: Available in the dev container
- **Use case**: Code development, experimentation, data analysis
- **Start**: `jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root`

### 2. Open WebUI (Chat Interface)
- **Access**: [http://localhost:3000](http://localhost:3000) (after setup)
- **Use case**: ChatGPT-style interaction with models
- **Setup**: See `.devcontainer/README.md` for detailed instructions

### 3. Direct API Access
- **Access**: `http://host.docker.internal:11434` (from container)
- **Use case**: Custom applications, scripts, integrations
- **Docs**: [Ollama API Reference](https://github.com/ollama/ollama/blob/main/docs/api.md)

## 🚦 Getting Started Workflows

### For Beginners
1. Open `notebooks/sample_query.ipynb`
2. Run the cells to test basic functionality
3. Experiment with different prompts and models
4. Try the Open WebUI for chat-style interaction

### For Developers
1. Explore the LangChain and LlamaIndex examples
2. Create custom model configurations in `models/`
3. Build RAG (Retrieval Augmented Generation) applications
4. Integrate with vector databases and embeddings

### For Researchers
1. Document experiments in `models/llama3/notes.md`
2. Create systematic prompt engineering workflows
3. Track model performance across different configurations
4. Use Jupyter notebooks for analysis and visualization

## 🔍 Common Tasks

### Download and Use a New Model
```bash
# Download a model
ollama pull codellama

# Test it
ollama run codellama "Write a Python function to sort a list"

# Use in Python
llm = Ollama(model="codellama")
```

### Create a Custom Model
1. Edit `models/llama3/Modelfile`
2. Build: `ollama create my-model -f ./models/llama3/Modelfile`
3. Test: `ollama run my-model`

### Set Up RAG (Retrieval Augmented Generation)
1. Use ChromaDB for document storage
2. Create embeddings with Sentence Transformers
3. Combine with LangChain for context-aware responses

### Export and Share Models
```bash
# Save model configuration
ollama show my-model --modelfile > my-model.Modelfile

# Share the Modelfile with others
```

## 🛠️ Troubleshooting

### Common Issues

**Can't connect to Ollama:**
- Ensure Ollama is running: `ollama serve`
- Test connection: `curl http://localhost:11434`

**Container won't start:**
- Check Docker resources
- Rebuild container: Command Palette → "Dev Containers: Rebuild Container"

**Models not working:**
- Verify model is downloaded: `ollama list`
- Check model name in environment variables

### Getting Help
- Check `.devcontainer/README.md` for detailed setup instructions
- Review model-specific notes in `models/llama3/notes.md`
- Visit [Ollama GitHub](https://github.com/ollama/ollama) for official documentation

## 🚀 Next Steps

### Extend the Environment
- Add more AI libraries to `requirements.txt`
- Create specialized model configurations
- Build custom applications and tools

### Experiment and Learn
- Try different model architectures
- Explore advanced prompt engineering
- Build real-world AI applications

### Contribute
- Document your experiments
- Share useful model configurations
- Create example notebooks for common use cases

## 📄 License

This project is provided as-is for educational and development purposes. Please respect the licenses of the underlying tools and models used.

---

**Happy AI Development!** 🤖

*For detailed setup instructions, see [`.devcontainer/README.md`](.devcontainer/README.md)*
