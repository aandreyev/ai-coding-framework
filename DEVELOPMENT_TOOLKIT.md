# The Developer's Toolkit: A Guide to Our High-Efficiency Environment

This document is the single source of truth for setting up and maintaining our standardized, high-performance development environment. Its purpose is to ensure every developer has access to the most effective tools, configured for maximum efficiency and seamless AI collaboration.

## 1. Core Philosophy

A standardized, powerful toolkit is the foundation of our development process. By ensuring every developer uses the same core tools and configurations, we:
- **Maximize Efficiency:** Reduce time spent on setup and troubleshooting.
- **Enhance Collaboration:** Ensure that our AI assistants have a consistent environment to work in.
- **Maintain Quality:** Enforce standards through pre-configured tools.

## 2. Local Machine Setup (macOS)

This section guides you through the initial setup of your local machine. These steps should be completed once.

### Step 1: Install Homebrew
Homebrew is our primary package manager for macOS.
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
source ~/.zshrc
```

### Step 2: Install Core Development Tools
```bash
# Install Git, Docker, and the GitHub CLI
brew install git docker gh

# Install language version managers
brew install nvm pyenv

# Install essential command-line utilities
brew install wget jq httpie bat exa fd ripgrep fzf tree
```

### Step 3: Configure Git and GitHub
```bash
# Configure Git identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main

# Authenticate with GitHub
gh auth login
```

## 3. IDE Configuration (Cursor)

Cursor is our primary IDE, chosen for its powerful AI integration.

### Step 1: Install Cursor
[Download and install Cursor from the official website.](https://cursor.sh/)

### Step 2: Recommended Settings
To ensure a consistent experience, configure the following settings in Cursor:
- **Auto Save:** On
- **Format on Save:** On
- **Git Integration:** Enabled
- **Editor: Default Formatter:** Prettier

### Step 3: Recommended Extensions
While Cursor is powerful out-of-the-box, these extensions can further enhance productivity:
- **Prettier - Code formatter:** For consistent code style.
- **ESLint:** For identifying and fixing problems in JavaScript.
- **GitLens — Git supercharged:** For better Git integration.

## 4. AI Enhancement (MCP Servers)

We use a standard set of MCP (Model Context Protocol) servers to extend the capabilities of our AI assistant.

### Step 1: Install the MCP Installer
```bash
npm install -g cursor-mcp-installer-free
```

### Step 2: Install the Standard Server Suite
This command will install our approved suite of MCP servers:
```bash
cursor-mcp-installer install @anthropic/mcp-server-filesystem \
  @anthropic/mcp-server-git \
  @anthropic/mcp-server-github \
  @anthropic/mcp-server-sqlite \
  @anthropic/mcp-server-docker \
  @anthropic/mcp-server-web-search \
  @anthropic/mcp-server-time
```

### Step 3: Configure MCP Access
No manual configuration is typically needed, as the installer handles this. You can verify the installed servers by running:
```bash
cursor-mcp-installer list
```

## 5. Project Environment

For individual projects, we use a consistent approach to environment management to ensure reproducibility and reliability.

### Virtual Environments
- **Principle:** Always use virtual environments to isolate dependencies.
- **Python:** Use `pyenv` for version management and `Poetry` for dependency management.
- **Node.js:** Use `nvm` for version management.

### Containerization
- **Principle:** Use Docker for local development to ensure parity with production.
- **Configuration:** Each project should have a `docker-compose.yml` file for defining the local environment.

For more details on project-specific setup, refer to the `ENVIRONMENT_SETUP.md` document. 