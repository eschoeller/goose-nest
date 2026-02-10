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
ansible-playbook playbook.yml
```

You'll be prompted for `sudo` password for tasks that need root (Ollama install, systemd config, model directory creation).

## What It Does

1. **gpu_verify** — Confirms NVIDIA GPU is present, reports VRAM/CUDA/driver info
2. **ollama** — Installs Ollama, moves model storage to `/games/models/ollama`, configures systemd
3. **models** — Pulls configured LLM models (sized for 12GB VRAM)
4. **goose** — Installs Goose CLI, configures Ollama as the LLM provider

## Configuration

Edit `group_vars/all.yml` to customize:

```yaml
ollama_models_dir: /games/models/ollama   # Where models are stored on disk
ollama_host: "http://localhost:11434"

ollama_models:                             # Models to pull
  - qwen2.5-coder:14b                     # ~9GB  - Coding model
  - qwen2.5:7b                            # ~4.7GB - General purpose
  - deepseek-r1:7b                        # ~4.7GB - Reasoning

goose_default_model: qwen2.5-coder:14b
```

## Running Individual Roles

Use tags to run specific parts:

```bash
ansible-playbook playbook.yml --tags gpu      # GPU check only
ansible-playbook playbook.yml --tags ollama    # Ollama setup only
ansible-playbook playbook.yml --tags models    # Pull models only
ansible-playbook playbook.yml --tags goose     # Goose setup only
```

## Verification

```bash
ansible-playbook playbook.yml --check   # Dry run
ollama list                              # Verify models
goose version                            # Verify Goose
goose session                            # Start interactive session
```
