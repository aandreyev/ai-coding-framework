# Foundation Setup Strategy

This document provides the foundational principles and verification procedures for setting up AI-assisted development environments. For step-by-step installation instructions, refer to [`DEVELOPMENT_TOOLKIT.md`](DEVELOPMENT_TOOLKIT.md).

## Foundation Philosophy

### 1. Environment Consistency
**Principle:** All developers should have identical core development environments to ensure reproducible builds and consistent AI behavior.

**Implementation Requirements:**
- Same versions of core tools (Node.js, Python, Docker)
- Identical configuration files and settings
- Standardized directory structures and naming conventions
- Consistent environment variable management

### 2. Security by Default
**Principle:** Development environments should be secure from initial setup, not as an afterthought.

**Security Requirements:**
- All credentials stored in secure key management systems
- Development databases isolated from production
- Network access restricted to necessary services only
- Regular security updates for all development tools

### 3. AI Optimization
**Principle:** Development environments should be optimized for AI assistant performance and context understanding.

**AI Requirements:**
- MCP servers configured for maximum AI capability
- Project structures that enhance AI code understanding
- Consistent naming and organization patterns
- Comprehensive documentation and metadata

## Pre-Setup Requirements

### 1. System Prerequisites
Before beginning setup, verify your system meets these requirements:

**macOS Requirements:**
- macOS 12.0 (Monterey) or later
- 8GB RAM minimum (16GB recommended for large projects)
- 50GB free disk space
- Admin access for tool installation
- Internet connection for package downloads

**Hardware Optimization:**
- SSD storage for faster file operations
- Multiple CPU cores for parallel processing
- Sufficient RAM for running containers and development tools

### 2. Account Prerequisites
Ensure you have access to these services:

**Required Accounts:**
- GitHub account with appropriate repository access
- Package manager accounts (npm, PyPI) if publishing packages
- Cloud service accounts (AWS, GCP, Azure) if using cloud resources
- CI/CD service accounts (GitHub Actions, etc.)

**Access Verification:**
```bash
# Verify GitHub access
ssh -T git@github.com

# Verify package manager access
npm whoami
pip list --user

# Verify cloud CLI access (if applicable)
aws sts get-caller-identity
gcloud auth list
```

## Setup Validation Framework

### 1. Environment Validation Checklist

**Core Tools Verification:**
```bash
# Verify tool installations
echo "=== Tool Version Check ==="
git --version
node --version
npm --version
python --version
pip --version
docker --version
docker-compose --version

# Verify package managers
brew --version
curl --version
wget --version

# Verify development tools
code --version  # VS Code or Cursor
```

**Configuration Verification:**
```bash
# Verify Git configuration
git config --global --list

# Verify Docker functionality
docker run hello-world

# Verify Node.js functionality
node -e "console.log('Node.js is working')"

# Verify Python functionality
python -c "print('Python is working')"
```

### 2. MCP Integration Validation

**MCP Server Health Check:**
```bash
# Verify MCP installer
cursor-mcp-installer --version

# List installed MCP servers
cursor-mcp-installer list

# Test MCP server connectivity
cursor-mcp-installer status
```

**AI Assistant Integration Test:**
Create a test file to verify AI can access your environment:

```javascript
// test-ai-integration.js
/**
 * Test file for AI assistant integration
 * This file helps verify that AI can understand and work with your environment
 */

const testConfig = {
  environment: 'development',
  aiIntegration: true,
  mcpServers: [
    'filesystem',
    'git',
    'github',
    'docker'
  ]
};

function validateEnvironment() {
  console.log('Environment validation starting...');
  
  // Test file system access
  const fs = require('fs');
  const path = require('path');
  
  try {
    const packageJson = JSON.parse(
      fs.readFileSync(path.join(process.cwd(), 'package.json'), 'utf8')
    );
    console.log('✓ File system access working');
    console.log('✓ Project structure accessible');
  } catch (error) {
    console.error('✗ File system access issue:', error.message);
  }
  
  // Test environment variables
  console.log('Environment check complete');
  return testConfig;
}

module.exports = { validateEnvironment, testConfig };
```

### 3. Project Structure Validation

**Standard Directory Structure:**
```
project-root/
├── .env.example              # Environment variable template
├── .gitignore               # Git ignore patterns
├── .github/                 # GitHub workflows and templates
│   └── workflows/          # CI/CD workflows
├── docs/                   # Project documentation
│   ├── api/               # API documentation
│   └── setup/             # Setup and deployment guides
├── src/                    # Source code
│   ├── components/        # Reusable components
│   ├── services/          # Business logic
│   ├── utils/             # Utility functions
│   └── config/            # Configuration files
├── tests/                  # Test files
│   ├── unit/              # Unit tests
│   ├── integration/       # Integration tests
│   └── e2e/               # End-to-end tests
├── scripts/               # Build and deployment scripts
└── package.json           # Project dependencies and scripts
```

**Directory Creation Script:**
```bash
#!/bin/bash
# create-project-structure.sh

echo "Creating standard project structure..."

# Create main directories
mkdir -p src/{components,services,utils,config}
mkdir -p tests/{unit,integration,e2e}
mkdir -p docs/{api,setup}
mkdir -p scripts
mkdir -p .github/workflows

# Create essential files
touch .env.example
touch .gitignore
touch README.md
touch package.json

echo "✓ Project structure created"
echo "✓ Essential files initialized"
```

## Common Setup Issues and Solutions

### 1. Permission Issues

**Problem:** Installation fails due to permission errors
**Solution:**
```bash
# Fix npm permissions
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Fix Docker permissions (Linux)
sudo usermod -aG docker $USER
newgrp docker
```

### 2. Path Configuration Issues

**Problem:** Commands not found after installation
**Solution:**
```bash
# Check current PATH
echo $PATH

# Add common tool paths to shell configuration
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 3. Version Conflicts

**Problem:** Multiple versions of tools causing conflicts
**Solution:**
```bash
# Use version managers for consistency
# Node.js version management
nvm install --lts
nvm use --lts
nvm alias default node

# Python version management  
pyenv install 3.11.7
pyenv global 3.11.7
```

### 4. MCP Server Issues

**Problem:** MCP servers not connecting or functioning
**Solution:**
```bash
# Reinstall MCP servers
cursor-mcp-installer uninstall @anthropic/mcp-server-filesystem
cursor-mcp-installer install @anthropic/mcp-server-filesystem

# Check configuration
cat ~/.cursor/mcp.json

# Restart Cursor after MCP changes
```

## Security Hardening for Development

### 1. Credential Management

**Secure Credential Storage:**
```bash
# Use environment variables, never hardcode secrets
echo "API_KEY=your_api_key_here" >> .env.local

# Add .env files to .gitignore
echo ".env*" >> .gitignore
echo "!.env.example" >> .gitignore

# Use OS keychain for sensitive data
security add-generic-password -a "$USER" -s "github-token" -w "your_token"
```

### 2. Network Security

**Development Network Configuration:**
```bash
# Configure firewall for development
sudo ufw enable
sudo ufw allow 3000  # Development server
sudo ufw allow 5432  # PostgreSQL
sudo ufw allow 6379  # Redis

# Block unnecessary ports
sudo ufw deny 22  # SSH (if not needed)
```

### 3. Code Security

**Security Scanning Setup:**
```bash
# Install security scanning tools
npm install -g audit-ci
pip install safety bandit

# Add security checks to package.json
# "scripts": {
#   "security-check": "npm audit && safety check && bandit -r src/"
# }
```

## Foundation Verification Script

Create this script to validate your complete foundation setup:

```bash
#!/bin/bash
# foundation-verify.sh

echo "🔍 AI Coding Framework Foundation Verification"
echo "=============================================="

# Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Test function
test_command() {
    if command -v $1 &> /dev/null; then
        echo -e "${GREEN}✓${NC} $1 is installed"
        return 0
    else
        echo -e "${RED}✗${NC} $1 is not installed"
        return 1
    fi
}

# Core tools check
echo "📋 Checking core development tools..."
test_command git
test_command node
test_command npm
test_command python
test_command docker
test_command curl

# MCP integration check
echo "🤖 Checking AI integration..."
test_command cursor-mcp-installer

# Environment check
echo "🔧 Checking environment configuration..."
if [ -f ".env.example" ]; then
    echo -e "${GREEN}✓${NC} .env.example found"
else
    echo -e "${YELLOW}⚠${NC} .env.example not found"
fi

if [ -f ".gitignore" ]; then
    echo -e "${GREEN}✓${NC} .gitignore found"
else
    echo -e "${YELLOW}⚠${NC} .gitignore not found"
fi

# Git configuration check
echo "📝 Checking Git configuration..."
git_name=$(git config --global user.name)
git_email=$(git config --global user.email)

if [ -n "$git_name" ]; then
    echo -e "${GREEN}✓${NC} Git user.name configured: $git_name"
else
    echo -e "${RED}✗${NC} Git user.name not configured"
fi

if [ -n "$git_email" ]; then
    echo -e "${GREEN}✓${NC} Git user.email configured: $git_email"
else
    echo -e "${RED}✗${NC} Git user.email not configured"
fi

echo "=============================================="
echo "🎉 Foundation verification complete!"
echo "Next step: Review DEVELOPMENT_TOOLKIT.md for detailed setup"
```

## Next Steps

After completing foundation setup:

1. **Tool Configuration:** Follow [`DEVELOPMENT_TOOLKIT.md`](DEVELOPMENT_TOOLKIT.md) for detailed tool setup
2. **Environment Testing:** Run the foundation verification script
3. **Project Creation:** Use the project structure template for new projects
4. **AI Integration:** Test MCP server functionality with Cursor
5. **Security Review:** Implement security hardening measures
6. **Team Onboarding:** Share this setup with team members

## Maintenance and Updates

### Regular Maintenance Tasks

**Weekly:**
- Update development tools to latest stable versions
- Review and update environment variables
- Check for security vulnerabilities in dependencies

**Monthly:**
- Update MCP servers to latest versions
- Review and update development tool configurations
- Audit development environment security settings

**Quarterly:**
- Full environment rebuild and testing
- Review and update foundation setup procedures
- Update documentation based on lessons learned

For ongoing maintenance guidance, see [`LEARNINGS.md`](LEARNINGS.md) to track and improve the foundation setup process. 