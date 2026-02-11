# goose-nest

Local AI workstation setup — **Goose** (Block's AI agent) + **Ollama** (local LLM inference) on NVIDIA GPU, managed with Ansible.

## Prerequisites

- Ubuntu/Debian with NVIDIA GPU and drivers installed
- CUDA toolkit
- Ansible 2.18+
- `gh` CLI (for repo management)

## Quick Start

```bash
git clone https://github.com/eschoeller/goose-nest.git
cd goose-nest
ansible-playbook deploy.yml -K
```

You'll be prompted for `sudo` password for tasks that need root (Ollama install, systemd config, model directory creation).

## Post-Install: Configure Goose

After the playbook completes, you need to configure Goose manually:

### 1. Select provider and model

```bash
goose configure
```

- **Provider**: Ollama
- **Model**: `qwen3-coder:30b` (recommended) or `qwen2.5-coder:14b`
- **Telemetry**: your choice

### 2. Enable extensions

By default, Goose has very few extensions enabled. For full agentic capabilities (file access, shell commands, code editing), enable these extensions via `goose configure`:

| Extension | What it provides |
|-----------|-----------------|
| **developer** | Shell access and code editing (essential) |
| **code_execution** | Token-efficient tool calls via code |
| **computercontroller** | Web scraping, file caching, automations |
| **memory** | Persistent memory across sessions |
| **chatrecall** | Search past conversations |
| **autovisualiser** | Data visualization and UI generation |

Without the **developer** extension, Goose cannot read files or run commands — it will only be able to chat.

### 3. Verify

```bash
goose session
```

Ask Goose to read a file or run a command to confirm extensions are working.

### Config location

Goose stores its configuration at `~/.config/goose/config.yaml`.

## What It Does

1. **gpu_verify** — Confirms NVIDIA GPU is present, reports VRAM/CUDA/driver info
2. **ollama** — Installs/upgrades Ollama, moves model storage to `/games/models/ollama`, configures systemd
3. **models** — Pulls configured LLM models
4. **goose** — Installs/upgrades Goose CLI

Both Ollama and Goose are automatically upgraded when new versions are available.

## Configuration

Edit `group_vars/all.yml` to customize:

```yaml
ollama_models_dir: /games/models/ollama   # Where models are stored on disk
ollama_host: "http://localhost:11434"

ollama_models:                             # Models to pull
  - qwen3-coder:30b                       # ~19GB - MoE, best tool-calling
  - qwen2.5-coder:14b                     # ~9GB  - Dense coding model
  - qwen2.5:7b                            # ~4.7GB - General purpose
  - deepseek-r1:7b                        # ~4.7GB - Reasoning

goose_default_model: qwen3-coder:30b
```

## Running Individual Roles

Use tags to run specific parts:

```bash
ansible-playbook deploy.yml --tags gpu      # GPU check only
ansible-playbook deploy.yml --tags ollama    # Ollama setup only
ansible-playbook deploy.yml --tags models    # Pull models only
ansible-playbook deploy.yml --tags goose     # Goose setup only
```

## Verification

```bash
ansible-playbook deploy.yml --check   # Dry run
ollama list                           # Verify models
goose -V                              # Verify Goose version
goose session                         # Start interactive session
```
