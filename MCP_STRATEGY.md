# MCP Strategy (Model Context Protocol)

This document defines our approach to using Model Context Protocol (MCP) servers to extend the AI assistant's capabilities. MCP servers act as standardized bridges between the AI and external tools, services, and data sources.

**Related Documents:**
- `coding_principles.md` - "Context is the Fuel" principle - MCP servers provide enhanced context
- `FOUNDATION_SETUP.md` - **Installation location** - MCP server setup and configuration
- `TESTING_STRATEGY.md` - MCP servers assist with test generation and analysis
- `LOGGING_STRATEGY.md` - MCP servers can analyze structured logs for debugging
- `ENVIRONMENT_SETUP.md` - MCP integration with development environment
- `PROJECT_TEMPLATES.md` - MCP configuration templates

**Core Purpose:** Enhance AI assistant capabilities while maintaining the human-as-architect, AI-as-builder philosophy from our coding principles.

## What is MCP?

Model Context Protocol (MCP) is a standardized way for AI models to connect to external tools through dedicated server applications. Unlike built-in tools, MCP servers are:

- **Modular**: Install only what you need
- **Extensible**: Create custom servers for specific workflows  
- **Standardized**: Follow a common protocol for consistency
- **Community-driven**: Built and maintained by the developer community

## Architecture Overview

```
AI Model <--> MCP Client (Cursor) <--> MCP Server <--> External Tool/Service
```

The MCP client in Cursor discovers available servers and their capabilities, then routes requests from the AI to the appropriate server.

## Recommended MCP Servers for Development

### Core Development Stack

1. **Git MCP Server**
   - **Purpose**: Advanced git operations beyond basic commands
   - **Capabilities**: Branch management, merge conflict resolution, commit history analysis
   - **Use Case**: Complex git workflows, repository analysis

2. **Database MCP Server** 
   - **Purpose**: Direct database interaction
   - **Capabilities**: Query execution, schema inspection, data analysis
   - **Use Case**: Database debugging, schema migrations, data exploration

3. **File System MCP Server**
   - **Purpose**: Advanced file operations
   - **Capabilities**: File watching, bulk operations, permission management
   - **Use Case**: Large-scale refactoring, file organization

### Enhancement Stack

4. **Web Search MCP Server**
   - **Purpose**: Real-time information gathering
   - **Capabilities**: Search engines, documentation lookup, library research
   - **Use Case**: Finding solutions, checking latest versions, researching APIs

5. **GitHub MCP Server**
   - **Purpose**: GitHub platform integration
   - **Capabilities**: Issue management, PR operations, repository analysis
   - **Use Case**: Project management, code review automation

6. **Docker MCP Server**
   - **Purpose**: Container management
   - **Capabilities**: Image building, container orchestration, environment setup
   - **Use Case**: Development environment consistency, deployment preparation

## Configuration Strategy

### Development Environment
- **Local MCP Servers**: Install servers that work with local tools (git, file system, database)
- **Configuration**: Store MCP server configs in project `.mcp/` directory
- **Documentation**: Document active MCP servers in `ENVIRONMENT_SETUP.md`

### Production Considerations
- **Security**: Limit MCP server permissions in production environments
- **Monitoring**: Log MCP server usage for debugging and optimization
- **Fallbacks**: Ensure core functionality works without MCP servers

## Best Practices

### Server Selection
- **Start Small**: Begin with 2-3 essential servers
- **Evaluate Impact**: Measure how each server improves workflow
- **Regular Review**: Periodically assess which servers are actually used

### Security
- **Principle of Least Privilege**: Grant minimal necessary permissions
- **Credential Management**: Use secure credential storage for server authentication
- **Network Security**: Restrict server network access where possible

### Maintenance
- **Version Control**: Track MCP server versions and configurations
- **Updates**: Regularly update servers for security and feature improvements
- **Backup**: Maintain fallback procedures when servers are unavailable

## Integration with Our Workflow

MCP servers enhance our existing workflow by:

1. **Expanding Context**: Servers provide richer information for AI decision-making
2. **Automating Tasks**: Direct tool integration reduces manual copy-paste operations  
3. **Improving Accuracy**: Real-time data access leads to more current and accurate assistance
4. **Streamlining Debugging**: Direct access to logs, databases, and system state

MCP servers work alongside our existing principles in `coding_principles.md`, providing the AI with more powerful tools while maintaining human oversight and control.

## Recommended MCP Servers for Our Environment

Based on current research and our development workflow, here are the specific MCP servers we should provision:

### Core Development Stack (Priority 1)

**1. Cursor MCP Installer** - `cursor-mcp-installer-free`
- **Purpose**: Simplifies installation of other MCP servers
- **Installation**: `npm install -g cursor-mcp-installer-free`
- **Why First**: Makes installing other servers much easier
- **Configuration**: 
  ```json
  {
    "mcpServers": {
      "MCP Installer": {
        "command": "cursor-mcp-installer-free",
        "type": "stdio",
        "args": ["index.mjs"]
      }
    }
  }
  ```

**2. GitHub MCP Server** - `@modelcontextprotocol/server-github`
- **Purpose**: Repository analysis, issue management, PR operations
- **Installation**: Via MCP Installer or `npm install -g @modelcontextprotocol/server-github`
- **Use Case**: Aligns with our version control principles
- **Requires**: GitHub personal access token

**3. File System MCP Server** - `@modelcontextprotocol/server-filesystem`
- **Purpose**: Advanced file operations, bulk operations
- **Installation**: Via MCP Installer
- **Use Case**: Large-scale refactoring, file organization
- **Security**: Configure with specific directory permissions

### Database & Infrastructure Stack (Priority 2)

**4. PostgreSQL MCP Server** - Various implementations available
- **Purpose**: Database querying, schema inspection
- **Installation**: Multiple options on MCPHub
- **Use Case**: Database debugging, migrations
- **Requires**: Database connection credentials

**5. Docker MCP Server** - Community implementations
- **Purpose**: Container management, environment consistency
- **Installation**: Via MCP Installer
- **Use Case**: Supports our Docker-based development principles

### Enhancement Stack (Priority 3)

**6. Web Search MCP Server** - `mcp-server-fetch` or similar
- **Purpose**: Real-time information gathering
- **Installation**: Via MCP Installer
- **Use Case**: Research, documentation lookup, API discovery

**7. OpenAPI MCP Server** - `mcp-server-openapi`
- **Purpose**: API exploration and testing
- **Installation**: Via MCP Installer
- **Use Case**: API development and integration

## Installation and Setup

### Prerequisites (macOS)

1. **Install Homebrew** (if not already installed):
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install Node.js** via Homebrew:
   ```bash
   brew install node
   ```

3. **Install the MCP Installer** (simplifies server management):
   ```bash
   npm install -g cursor-mcp-installer-free
   ```

### Installing Our Recommended Server Stack

Use the MCP installer to set up our core servers:

```bash
# Core Development Servers
cursor-mcp-installer install @anthropic/mcp-server-filesystem
cursor-mcp-installer install @anthropic/mcp-server-git  
cursor-mcp-installer install @anthropic/mcp-server-github

# Database and Infrastructure
cursor-mcp-installer install @anthropic/mcp-server-sqlite
cursor-mcp-installer install @anthropic/mcp-server-docker

# Utility Servers
cursor-mcp-installer install @anthropic/mcp-server-web-search
cursor-mcp-installer install @anthropic/mcp-server-time
```

### Mac-Specific Configuration

**Docker Integration:** Ensure Docker Desktop for Mac is running:
```bash
# Check Docker is running
docker --version
docker compose version
```

**File System Permissions:** Grant necessary permissions for filesystem access:
- Go to System Preferences → Security & Privacy → Privacy → Files and Folders
- Ensure Cursor has access to the directories you'll be working in

**GitHub Integration:** Configure GitHub CLI for seamless integration:
```bash
brew install gh
gh auth login
```

## Configuration Management

### Cursor Configuration File Location
- **macOS/Linux**: `~/.cursor/mcp.json`
- **Windows**: `%USERPROFILE%\.cursor\mcp.json`

### Security Best Practices
- Store sensitive credentials in environment variables
- Use `.env` files for development
- Implement least-privilege access for file system servers
- Regular security audits of installed servers

### Monitoring and Maintenance
- Track server performance and usage
- Regular updates of server packages
- Monitor for security vulnerabilities
- Document server configurations in project README

## Integration with Our Existing Workflow

MCP servers complement our established practices:

1. **Environment Parity**: Servers work consistently across dev/staging/prod
2. **Logging Strategy**: Server actions are logged and traceable
3. **AI Collaboration**: Servers enhance the 4-stage AI workflow
4. **Documentation**: Server configurations are version-controlled 