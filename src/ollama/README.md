# 🤖 Ollama Service

A modular Docker Compose configuration system for Ollama with support for multiple environments and extensions.

## 🏗️ Project Structure

```sh
src/
├── components/                              # Source compose components
│   ├── base/                               # Base components
│   │   ├── docker-compose.yml              # Main Ollama service
│   │   └── .env.example                    # Base environment variables
│   ├── environments/                       # Environment components
│   │   ├── devcontainer/
│   │   │   └── docker-compose.yml          # Dev container integration
│   │   ├── forwarding/
│   │   │   └── docker-compose.yml          # Development with port forwarding
│   │   ├── internal/
│   │   │   └── docker-compose.yml          # Internal network only
│   │   ├── letsencrypt/
│   │   │   ├── docker-compose.yml          # Let's Encrypt SSL automation
│   │   │   └── .env.example                # SSL configuration variables
│   │   └── step-ca/
│   │       ├── docker-compose.yml          # Step CA private SSL automation
│   │       └── .env.example                # Private CA configuration variables
│   └── extensions/                         # Extension components
│       ├── nvidia/                         # NVIDIA GPU support extension
│       │   └── docker-compose.yml          # GPU acceleration configuration
│       └── .gitkeep                        # Placeholder for future extensions
├── build/                        # Generated configurations (auto-generated)
│   ├── devcontainer/
│   │   ├── base/                 # Dev container + base configuration
│   │   └── nvidia/               # Dev container + NVIDIA GPU support
│   ├── forwarding/
│   │   ├── base/                 # Development + base configuration
│   │   └── nvidia/               # Development + NVIDIA GPU support
│   ├── internal/
│   │   ├── base/                 # Internal network + base configuration
│   │   └── nvidia/               # Internal network + NVIDIA GPU support
│   ├── letsencrypt/
│   │   ├── base/                 # Let's Encrypt SSL + base configuration
│   │   └── nvidia/               # Let's Encrypt SSL + NVIDIA GPU support
│   └── step-ca/
│       ├── base/                 # Step CA private SSL + base configuration
│       └── nvidia/               # Step CA private SSL + NVIDIA GPU support
├── build.sh                      # Build script
└── README.md                     # This file
```

## 🚀 Quick Start

### 1. Build Configurations

Run the build script to generate all possible combinations:

```bash
./build.sh
```

This will create all combinations in the `build/` directory.

### 2. Choose Your Configuration

Navigate to the desired configuration directory:

```bash
# For development with port forwarding
cd build/forwarding/base/

# For development with NVIDIA GPU support
cd build/forwarding/nvidia/

# For dev container integration
cd build/devcontainer/base/

# For dev container with NVIDIA GPU support
cd build/devcontainer/nvidia/

# For internal network only
cd build/internal/base/

# For internal network with NVIDIA GPU support
cd build/internal/nvidia/

# For Let's Encrypt SSL automation
cd build/letsencrypt/base/

# For Step CA private SSL automation
cd build/step-ca/base/
```

### 3. Configure Environment

Copy and edit the environment file:

```bash
cp .env.example .env
# Edit .env with your values
```

### 4. Deploy

Start the services:

```bash
docker-compose up -d
```

Access: `http://localhost:11434` (for forwarding mode)

## 🔧 Available Configurations

### Environments

- **devcontainer**: Dev container integration for VS Code
- **forwarding**: Development environment with port forwarding (11434)
- **internal**: Internal network only, no port forwarding
- **letsencrypt**: SSL automation with Let's Encrypt certificates via nginx-proxy
- **step-ca**: Private SSL automation with Step CA certificates via nginx-proxy

### Extensions

- **nvidia**: NVIDIA GPU acceleration support for faster model inference

### Generated Combinations

Each environment can be combined with any extension:

- `devcontainer/base` - Dev container integration
- `devcontainer/nvidia` - Dev container with NVIDIA GPU support
- `forwarding/base` - Development with port forwarding
- `forwarding/nvidia` - Development with NVIDIA GPU support
- `internal/base` - Internal network only
- `internal/nvidia` - Internal network with NVIDIA GPU support
- `letsencrypt/base` - Let's Encrypt SSL automation
- `letsencrypt/nvidia` - Let's Encrypt SSL with NVIDIA GPU support
- `step-ca/base` - Step CA private SSL automation
- `step-ca/nvidia` - Step CA private SSL with NVIDIA GPU support

## 🔧 Environment Variables

### Base Configuration

- `COMPOSE_PROJECT_NAME`: Project name for Docker Compose (default: ollama)

### Ollama Configuration

- `OLLAMA_HOST`: Bind address for Ollama server (default: "0.0.0.0")
- `OLLAMA_KEEP_ALIVE`: How long to keep models loaded in memory (default: 5m)
- `OLLAMA_FLASH_ATTENTION`: Enable flash attention optimization (default: 0)

### SSL Configuration (letsencrypt/step-ca environments)

- `VIRTUAL_PORT`: Port for nginx-proxy virtual host (default: 11434)
- `VIRTUAL_HOST`: Domain name for the Ollama service (e.g., ollama.example.com)
- `LETSENCRYPT_HOST`: Domain for SSL certificate (should match VIRTUAL_HOST)
- `LETSENCRYPT_EMAIL`: Email for certificate registration and notifications

## 🤖 Using Ollama

### Initial Setup

1. **Start the service**:
   - Choose your desired configuration (with or without GPU support)
   - Start the container using Docker Compose

2. **Download models**:

   ```bash
   # Pull a model (example: Llama 2 7B)
   docker exec ollama ollama pull llama2

   # Pull other popular models
   docker exec ollama ollama pull codellama
   docker exec ollama ollama pull mistral
   docker exec ollama ollama pull neural-chat
   ```

3. **Verify installation**:

   ```bash
   # List downloaded models
   docker exec ollama ollama list

   # Check service status
   docker exec ollama ollama ps
   ```

### Accessing Ollama

```bash
# For forwarding mode
curl http://localhost:11434/api/generate -d '{
  "model": "llama2",
  "prompt": "Why is the sky blue?"
}'

# For SSL environments (letsencrypt/step-ca)
curl https://ollama.example.com/api/generate -d '{
  "model": "llama2",
  "prompt": "Why is the sky blue?"
}'

# For dev container and internal modes
# Access via internal network from other containers
```

### Using Models

1. **Interactive chat**:

   ```bash
   docker exec -it ollama ollama run llama2
   ```

2. **API requests**:

   ```bash
   # Generate text
   curl http://localhost:11434/api/generate -d '{
     "model": "llama2",
     "prompt": "Explain quantum computing",
     "stream": false
   }'

   # Chat completion
   curl http://localhost:11434/api/chat -d '{
     "model": "llama2",
     "messages": [
       {"role": "user", "content": "Hello!"}
     ]
   }'
   ```

3. **Model management**:

   ```bash
   # List available models
   docker exec ollama ollama list

   # Remove a model
   docker exec ollama ollama rm llama2

   # Show model information
   docker exec ollama ollama show llama2
   ```

## 🎮 GPU Acceleration

### NVIDIA GPU Support

The `nvidia` extension provides GPU acceleration for faster model inference:

- **Requirements**: NVIDIA GPU with CUDA support, NVIDIA Container Toolkit
- **Benefits**: Significantly faster model loading and inference
- **Usage**: Use configurations with `/nvidia` suffix

### Setup NVIDIA Support

1. **Install NVIDIA Container Toolkit**:

   ```bash
   # Ubuntu/Debian
   curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
   curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
     sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
     sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
   sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
   sudo systemctl restart docker
   ```

2. **Use GPU-enabled configuration**:

   ```bash
   cd build/forwarding/nvidia/
   docker-compose up -d
   ```

3. **Verify GPU usage**:

   ```bash
   # Check GPU utilization
   nvidia-smi

   # Check if Ollama detects GPU
   docker exec ollama ollama run llama2
   # Should show faster loading and inference times
   ```

## 🛠️ Development

### Adding New Environments

1. Create directory in `components/environments/` with `docker-compose.yml` and optional `.env.example` file
2. Run `./build.sh` to generate new combinations

### File Naming Convention

All component files follow the standard Docker Compose naming convention (`docker-compose.yml`) for:

- **VS Code compatibility**: Full support for Docker Compose language features and IntelliSense
- **IDE integration**: Proper syntax highlighting and validation in all major editors
- **Tool compatibility**: Works with Docker Compose plugins and extensions
- **Standard compliance**: Follows official Docker Compose file naming patterns

### Modifying Existing Components

1. Edit the component files in `components/`
2. Run `./build.sh` to regenerate configurations
3. The `build/` directory will be completely recreated

## 🌐 Networks

- **Development**: `ollama-network` (internal)
- **Dev container**: `ollama-workspace-network` (external)
- **Internal**: `ollama-network` (internal only)
- **Let's Encrypt**: `letsencrypt-network` (external, connects to nginx-proxy)
- **Step CA**: `step-ca-network` (external, connects to nginx-proxy with private CA)

## 🔒 Security

⚠️ **Production Checklist:**

- Secure API access with authentication
- Configure firewall rules for port 11434
- Regular security updates
- Monitor resource usage
- Limit model access appropriately

## 🆘 Troubleshooting

**Build Issues:**

- Ensure `yq` is installed: <https://github.com/mikefarah/yq#install>
- Check component file syntax
- Verify all required files exist

**Ollama Connection Issues:**

- Check if service is running: `docker logs ollama`
- Verify port 11434 is accessible
- Ensure sufficient disk space for models

**GPU Issues:**

- Verify NVIDIA drivers are installed: `nvidia-smi`
- Check NVIDIA Container Toolkit installation
- Ensure Docker has GPU access: `docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi`

**Model Issues:**

- Check available disk space: `docker exec ollama df -h`
- Verify model download: `docker exec ollama ollama list`
- Check model compatibility with your hardware

**Performance Issues:**

- Monitor resource usage: `docker stats ollama`
- Consider using GPU acceleration for better performance
- Adjust `OLLAMA_KEEP_ALIVE` for memory management

**SSL Issues:**

- Verify nginx-proxy is running and accessible
- Check domain DNS resolution: `nslookup ollama.example.com`
- Ensure external networks exist: `docker network ls | grep -E "(letsencrypt|step-ca)"`

## 📝 Notes

- The `build/` directory is automatically generated and should not be edited manually
- Environment variables in generated files use `$VARIABLE_NAME` format for proper interpolation
- Each generated configuration includes a complete `docker-compose.yml` and `.env.example`
- Missing `.env.*` files for components are handled gracefully by the build script
- Models are stored in the `ollama-data` volume and persist between container restarts
- GPU support requires NVIDIA Container Toolkit and compatible hardware

## 📚 Additional Resources

- [Official Ollama Documentation](https://ollama.ai/docs)
- [Ollama Model Library](https://ollama.ai/library)
- [Ollama API Reference](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
