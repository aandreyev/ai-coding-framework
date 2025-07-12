# Environment Setup Guide

This document provides the principles and high-level steps for setting up the development environment on **macOS**. Detailed setup scripts will be created for specific projects and language stacks.

**Related Documents:**
- `FOUNDATION_SETUP.md` - **Complete this first** - Essential machine setup
- `FUNCTIONAL_DESIGN_STRATEGY.md` - **Critical** - Business requirements and functional design process
- `coding_principles.md` - Core philosophy driving these environment choices
- `TESTING_STRATEGY.md` - Testing environment considerations
- `LOGGING_STRATEGY.md` - Observability requirements for environments
- `MCP_STRATEGY.md` - AI tool integration in development environment
- `PROJECT_TEMPLATES.md` - Implementation templates for specific projects
- `SECURITY_STRATEGY.md` - Secure environment configuration and secrets management
- `DEPLOYMENT_STRATEGY.md` - Environment parity and deployment consistency
- `DATA_MIGRATION_STRATEGY.md` - Environment data consistency and migrations
- `MONITORING_STRATEGY.md` - Environment monitoring and health checks

**Prerequisites:** Ensure `FOUNDATION_SETUP.md` is completed before applying these project environment strategies.

## 1. System Prerequisites

### Essential Tools (Install via Homebrew)

*   **Homebrew:** Package manager for macOS
*   **Git:** Version control  
*   **Docker Desktop:** Containerization and environment parity
*   **Node.js:** JavaScript runtime (via nvm for version management)
*   **Python:** Modern Python version (3.11+)

### Installation Approach
- Use Homebrew as the primary package manager
- Install Xcode Command Line Tools for development dependencies
- Configure Rosetta 2 on Apple Silicon machines for compatibility

## 2. Virtual Environment Strategy

### Core Principle
**Always use virtual environments** to ensure dependency isolation and reproducible builds. *(Supports Environment Parity principle from `coding_principles.md`)*

### Language-Specific Approaches
- **Python:** Poetry (preferred), virtualenv, or pipenv
- **Node.js:** nvm for version management + local node_modules
- **Ruby:** rbenv for version management + bundler
- **Java:** SDKMAN for version management
- **Go:** Built-in module system

### Best Practices
- Activate virtual environments before working
- Keep dependency files updated and committed
- Use version specification files (`.nvmrc`, `.python-version`)
- Commit lock files for reproducible builds

## 3. IDE Configuration

**Primary Editor:** Cursor for AI-assisted development *(Setup details in `FOUNDATION_SETUP.md`)*
- No special extensions required beyond defaults
- Configured for MCP server integration *(See `MCP_STRATEGY.md` for server details)*

## 4. MCP Server Integration

**Purpose:** Extend AI assistant capabilities with external tools *(Full strategy in `MCP_STRATEGY.md`)*

**Core Server Stack:**
- File system access
- Git integration  
- GitHub API access
- Database connectivity
- Docker management
- Web search capabilities

**Setup:** Use `cursor-mcp-installer` for streamlined installation *(Installation details in `FOUNDATION_SETUP.md`)*

## 5. Environment Parity Strategy

### Development-Production Consistency *(Core principle from `coding_principles.md`)*
- Use Docker containers for local development
- External secret management (Doppler, AWS Secrets Manager)
- Infrastructure as Code approach
- Same runtime environments across all stages

### Configuration Management
- Environment variables for configuration
- External secret stores for sensitive data
- `.env.example` files for local setup guidance *(Templates in `PROJECT_TEMPLATES.md`)*

## 6. Project-Specific Setup

When starting a new project, we will create:

### Setup Scripts (Language-Specific)
- `setup-python.sh` - Python virtual environment and dependencies
- `setup-node.sh` - Node.js version management and packages  
- `setup-ruby.sh` - Ruby version and gem management
- `setup-docker.sh` - Docker environment configuration

### Configuration Files
- `docker-compose.yml` - Multi-service development environment
- `Dockerfile` - Application containerization
- `.env.example` - Environment variable template
- Language-specific dependency files

### Common Commands Reference
- `commands.md` - Project-specific command reference
- Language and framework-specific usage patterns

## Implementation Notes

This guide establishes the **principles and strategy**. For each new project:

1. **Assessment:** Determine required language stack and tools
2. **Script Generation:** Create appropriate setup scripts  
3. **Configuration:** Generate project-specific config files
4. **Documentation:** Create project-specific command reference

This approach keeps our principles clean while providing automated setup for actual projects.

## Starting a New Project

When beginning a new project, follow these steps to implement our environment setup principles:

### 1. **Create Functional Design**
- **Complete functional design first** using `FUNCTIONAL_DESIGN_STRATEGY.md`
- Define business requirements and user needs
- Create detailed functional specifications
- Obtain stakeholder approval before proceeding

### 2. **Reference the Principles**
- Review `coding_principles.md` for strategic guidance
- Understand the "why" behind our approach
- Align project decisions with our core philosophy

### 3. **Copy Relevant Templates**
- Use `PROJECT_TEMPLATES.md` as your guide
- Select templates based on your language stack:
  - Python: `setup-python.sh`, Poetry/virtualenv configuration
  - Node.js: `setup-node.sh`, nvm and package management
  - Ruby: `setup-ruby.sh`, rbenv and bundler setup
  - Docker: `setup-docker.sh`, container orchestration

### 4. **Generate Project Documentation**
- Create project-specific `commands.md` reference
- Write focused `README.md` with quick start guide
- Document any project-specific troubleshooting
- Link to setup scripts for easy onboarding

### 5. **Validate Environment Parity**
- Ensure Docker configuration matches production
- Test that setup scripts create consistent environments
- Verify external secret management integration
- Confirm MCP servers work with project structure

This workflow ensures every new project starts with clear functional requirements and our proven technical principles while maintaining the flexibility to adapt to specific requirements.