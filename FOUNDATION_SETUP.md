# Foundation Development Setup

This document covers the essential tools and configuration needed for our AI-assisted development environment. Complete this setup once on each machine before starting any project work.

**Related Documents:**
- `coding_principles.md` - Core philosophy and workflow principles
- `ENVIRONMENT_SETUP.md` - Project-specific environment strategy
- `MCP_STRATEGY.md` - AI tool enhancement (MCP servers covered here)
- `TESTING_STRATEGY.md` - Testing tools and frameworks
- `PROJECT_TEMPLATES.md` - Project structure templates

**Setup Sequence:** Complete this foundation setup first, then refer to `ENVIRONMENT_SETUP.md` for project-specific configuration.

## Overview

This foundation setup provides:
- **Core development tools** (editors, version control, containerization)
- **AI assistance tools** (Cursor, Gemini CLI, MCP servers)
- **System configuration** (shell, package managers, permissions)
- **Development utilities** (language runtimes, version managers)

## 1. System Foundation (macOS)

### Package Manager
```bash
# Install Homebrew (if not already installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Add Homebrew to PATH
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
source ~/.zshrc
```

### Essential System Tools
```bash
# Development prerequisites
xcode-select --install

# Core development tools
brew install git curl wget jq

# Shell enhancements
brew install zsh-autosuggestions zsh-syntax-highlighting
```

### Apple Silicon Compatibility
```bash
# Install Rosetta 2 for compatibility (Apple Silicon only)
softwareupdate --install-rosetta
```

## 2. Primary Development Tools

### Cursor IDE
1. **Download and Install:** [Download Cursor](https://cursor.sh/)
2. **Initial Configuration:**
   - Sign in with your account
   - Enable AI features
   - Configure file access permissions (System Preferences → Privacy & Security)
3. **Essential Settings:**
   - Enable auto-save
   - Configure Git integration
   - Set up workspace preferences

### Docker Desktop
```bash
# Install Docker Desktop
brew install --cask docker

# Start Docker Desktop and complete setup
# Verify installation
docker --version
docker compose version
```

### Git Configuration
```bash
# Configure Git identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Configure Git defaults
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --global core.editor "cursor --wait"
```

### GitHub CLI
```bash
# Install GitHub CLI
brew install gh

# Authenticate with GitHub
gh auth login
# Follow prompts to authenticate via browser

# Verify authentication
gh auth status
```

## 3. Language Runtimes and Version Managers

### Node.js (via nvm)
```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Reload shell
source ~/.zshrc

# Install latest LTS Node.js
nvm install --lts
nvm use --lts
nvm alias default node

# Verify installation
node --version
npm --version
```

### Python (via pyenv)
```bash
# Install pyenv
brew install pyenv

# Add to shell configuration
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
source ~/.zshrc

# Install latest Python
pyenv install 3.11.7
pyenv global 3.11.7

# Install Poetry for dependency management
curl -sSL https://install.python-poetry.org | python3 -
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Ruby (via rbenv) - Optional
```bash
# Install rbenv
brew install rbenv

# Add to shell configuration
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc

# Install latest Ruby
rbenv install 3.2.0
rbenv global 3.2.0
```

## 4. AI Development Tools

### Gemini CLI
```bash
# Install Gemini CLI
npm install -g @google/generative-ai-cli

# Configure authentication
# Follow setup instructions for API key configuration
```

### MCP Server Infrastructure
```bash
# Install MCP installer
npm install -g cursor-mcp-installer-free

# Install core MCP servers
cursor-mcp-installer install @anthropic/mcp-server-filesystem
cursor-mcp-installer install @anthropic/mcp-server-git
cursor-mcp-installer install @anthropic/mcp-server-github
cursor-mcp-installer install @anthropic/mcp-server-sqlite
cursor-mcp-installer install @anthropic/mcp-server-docker
cursor-mcp-installer install @anthropic/mcp-server-web-search
cursor-mcp-installer install @anthropic/mcp-server-time

# Verify installation
cursor-mcp-installer list
```

## 5. Development Utilities

### Database Tools
```bash
# PostgreSQL client
brew install postgresql

# SQLite (usually pre-installed)
brew install sqlite

# Database GUI (optional)
brew install --cask tableplus
```

### Network and API Tools
```bash
# HTTP client
brew install httpie

# API testing
brew install --cask postman

# Network utilities
brew install nmap telnet
```

### Text Processing and Utilities
```bash
# Modern command-line tools
brew install bat exa fd ripgrep fzf tree

# JSON processing
brew install jq yq

# File compression
brew install unzip p7zip
```

## 6. Shell Configuration

### Zsh Enhancements
```bash
# Add useful aliases to ~/.zshrc
cat >> ~/.zshrc << 'EOF'

# Development aliases
alias ll='exa -la'
alias cat='bat'
alias find='fd'
alias grep='rg'

# Git aliases
alias gs='git status'
alias ga='git add'
alias gc='git commit'
alias gp='git push'
alias gl='git log --oneline'

# Docker aliases
alias dps='docker ps'
alias dimg='docker images'
alias dcp='docker compose'

# Directory navigation
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'

EOF

# Reload shell configuration
source ~/.zshrc
```

## 7. System Permissions and Security

### File Access Permissions
Configure system permissions for development tools:

1. **System Preferences → Privacy & Security → Files and Folders**
   - Grant Cursor access to Desktop, Documents, Downloads
   - Grant Terminal/iTerm access to required directories

2. **System Preferences → Privacy & Security → Developer Tools**
   - Allow Cursor and Terminal to run software locally

3. **Firewall Configuration**
   - Allow Docker and development servers through firewall
   - Configure localhost access for development

### SSH Configuration
```bash
# Generate SSH key for Git operations
ssh-keygen -t ed25519 -C "your.email@example.com"

# Add SSH key to ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Add SSH key to GitHub
gh ssh-key add ~/.ssh/id_ed25519.pub --title "Development Machine"
```

## 8. Verification and Testing

### Verify Installation
```bash
# Check core tools
git --version
docker --version
node --version
python --version
poetry --version

# Check AI tools
cursor --version
cursor-mcp-installer --version

# Check utilities
bat --version
exa --version
rg --version
```

### Test MCP Integration
1. Open Cursor
2. Create a new file
3. Test AI assistant with MCP server capabilities
4. Verify file system access and Git integration

## 9. Maintenance and Updates

### Regular Updates
```bash
# Update Homebrew and packages
brew update && brew upgrade

# Update Node.js packages
npm update -g

# Update Python packages
pip install --upgrade pip

# Update MCP servers
cursor-mcp-installer update
```

### Backup Configuration
- Export and backup shell configuration files
- Document any custom configurations
- Keep a record of installed tools and versions

## Troubleshooting

### Common Issues
- **Permission errors**: Check system privacy settings
- **Command not found**: Verify PATH configuration in ~/.zshrc
- **Docker issues**: Ensure Docker Desktop is running
- **Git authentication**: Re-run `gh auth login` if needed

### Getting Help
- Check tool-specific documentation
- Review error messages and logs
- Consult team knowledge base
- Test in clean environment if needed

---

**Note:** Complete this foundation setup once per machine. Individual projects will use this foundation plus project-specific configuration as outlined in `ENVIRONMENT_SETUP.md`. 