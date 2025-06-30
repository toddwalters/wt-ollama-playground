# Llama3 Fine-Tuned Model Customization Notes

## Overview

This directory contains customizations and fine-tuning configurations for the Llama3 model. The setup includes custom prompts, model files, and specialized notebooks for working with fine-tuned variants.

## Directory Structure

```
llama3/
├── Modelfile          # Custom Ollama model configuration
├── notebooks/         # Jupyter notebooks for model experimentation
├── prompts/          # Custom prompt templates and examples
└── notes.md          # This file - documentation and notes
```

## Environment Configuration

### Environment Variables (from .env)
- `OLLAMA_DEFAULT_MODEL=llama3-instruct` - Default model for API calls
- `OLLAMA_BASE_MODEL=llama3` - Base model identifier
- `WORKSPACE_MODELS_PATH=/workspace/models` - Path to model configurations

## Model Customization

### Modelfile Configuration

The `Modelfile` in this directory defines custom parameters for the Llama3 model:

**Key Customization Areas:**
- **System Prompts**: Custom instructions that shape model behavior
- **Temperature Settings**: Control randomness in responses
- **Context Length**: Define how much conversation history to maintain
- **Stop Sequences**: Custom tokens to end generation
- **Parameter Tuning**: Fine-tune model parameters for specific use cases

### Example Modelfile Structure
```dockerfile
FROM llama3

# Custom system prompt
SYSTEM """You are a helpful AI assistant specialized in [your domain]."""

# Parameter customizations
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER repeat_penalty 1.1
PARAMETER num_ctx 4096

# Custom stop sequences
PARAMETER stop "</think>"
PARAMETER stop "[END]"
```

## Fine-Tuning Approaches

### 1. Prompt Engineering
- **System Prompts**: Define consistent behavior and personality
- **Few-Shot Examples**: Include examples in prompts for better performance
- **Chain-of-Thought**: Structure prompts to encourage step-by-step reasoning

### 2. Parameter Adjustment
- **Temperature**: Lower (0.1-0.3) for factual, higher (0.7-1.0) for creative
- **Top-p**: Controls nucleus sampling (0.9 recommended for most cases)
- **Repeat Penalty**: Prevents repetitive output (1.1-1.2 range)

### 3. Context Management
- **Context Length**: Balance between memory and performance
- **Context Templates**: Structure how conversation history is formatted

## Custom Prompts

### Specialized Prompt Templates

**Code Assistant Prompt:**
```
You are an expert software developer. When asked about code:
1. Provide clean, well-commented code
2. Explain your reasoning
3. Include error handling when appropriate
4. Suggest best practices
```

**Technical Writer Prompt:**
```
You are a technical documentation specialist. When writing:
1. Use clear, concise language
2. Include practical examples
3. Structure information logically
4. Consider the target audience's expertise level
```

### Prompt Categories
- **Domain-Specific**: Tailored for specific fields (medical, legal, technical)
- **Task-Oriented**: Focused on particular tasks (coding, writing, analysis)
- **Personality-Based**: Different interaction styles (formal, casual, educational)

## Usage Patterns

### Development Workflow
1. **Edit Modelfile**: Modify parameters and system prompts
2. **Build Custom Model**: `ollama create custom-llama3 -f Modelfile`
3. **Test in Notebooks**: Use Jupyter notebooks for experimentation
4. **Iterate**: Refine based on results

### Testing and Validation
- Use notebooks for systematic testing
- Document prompt effectiveness
- Track parameter impact on outputs
- Maintain version history of configurations

## Performance Optimization

### Memory Management
- **Model Size**: Consider quantized versions for resource constraints
- **Context Windows**: Optimize for your use case
- **Batch Processing**: Group similar requests

### Response Quality
- **Prompt Iteration**: Continuously refine prompts
- **Output Validation**: Test against expected outcomes
- **Feedback Loops**: Incorporate user feedback

## Integration with Dev Environment

### Ollama Commands
```bash
# Build custom model from Modelfile
ollama create llama3-custom -f ./models/llama3/Modelfile

# Test the custom model
ollama run llama3-custom

# List available models
ollama list

# Remove a model
ollama rm llama3-custom
```

### Python Integration
```python
import requests
import os

# Use environment variables
base_url = os.getenv('OLLAMA_API_BASE_URL')
default_model = os.getenv('OLLAMA_DEFAULT_MODEL')

# Make API calls
response = requests.post(f"{base_url}/api/generate", json={
    "model": default_model,
    "prompt": "Your prompt here",
    "stream": False
})
```

## Best Practices

### Model Development
1. **Version Control**: Track changes to Modelfile and prompts
2. **Documentation**: Document why changes were made
3. **Testing**: Validate changes before deploying
4. **Backup**: Keep working configurations safe

### Prompt Engineering
1. **Specificity**: Be specific about desired outputs
2. **Context**: Provide relevant context and examples
3. **Constraints**: Set clear boundaries and limitations
4. **Evaluation**: Test prompts with edge cases

### Performance Monitoring
1. **Response Times**: Monitor generation speed
2. **Resource Usage**: Track memory and CPU usage
3. **Quality Metrics**: Evaluate output quality consistently
4. **User Feedback**: Collect and incorporate feedback

## Common Issues and Solutions

### Model Not Loading
- Check Modelfile syntax
- Verify base model availability
- Review resource constraints

### Poor Performance
- Adjust temperature and top_p parameters
- Refine system prompts
- Optimize context length

### Inconsistent Outputs
- Add more specific constraints
- Include better examples
- Adjust repeat_penalty

## Experiment Log

### Model Variations Tested
| Date | Model Name | Configuration | Performance Notes |
|------|------------|---------------|-------------------|
| 2025-06-30 | llama3-base | Default settings | Baseline performance |
| | | | |

### Prompt Experiments
| Date | Prompt Type | Success Rate | Notes |
|------|-------------|--------------|-------|
| 2025-06-30 | Code Assistant | TBD | Initial setup |
| | | | |

## Future Improvements

### Planned Enhancements
- [ ] Domain-specific fine-tuning
- [ ] Custom training data integration
- [ ] Performance benchmarking suite
- [ ] Automated prompt optimization

### Research Areas
- Advanced prompt engineering techniques
- Multi-model ensemble approaches
- Custom loss functions for specific tasks
- Integration with external knowledge bases

## Resources

### Documentation
- [Ollama Modelfile Reference](https://github.com/ollama/ollama/blob/main/docs/modelfile.md)
- [Llama3 Model Documentation](https://llama.meta.com/)
- [Fine-tuning Best Practices](https://huggingface.co/docs/transformers/training)

### Tools and Libraries
- Ollama Python client
- Transformers library
- LangChain for prompt management
- Jupyter notebooks for experimentation

## Changelog

### 2025-06-30
- Initial setup of llama3 customization directory
- Created base Modelfile structure
- Established documentation framework
- Set up environment configuration

---

*Last updated: June 30, 2025*
*Maintainer: Development Team*
