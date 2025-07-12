# Project Template Structure

This document outlines how we organize project-specific setup files, keeping implementation details separate from our core principles.

**Related Documents:**
- `coding_principles.md` - "Documentation vs Implementation" principle drives this approach
- `ENVIRONMENT_SETUP.md` - **Primary reference** - Environment principles implemented here
- `TESTING_STRATEGY.md` - Test configuration templates and setup
- `LOGGING_STRATEGY.md` - Logging configuration templates
- `MCP_STRATEGY.md` - MCP server configuration templates
- `FOUNDATION_SETUP.md` - Required foundation before using these templates

**Purpose:** Provide concrete implementation templates that embody the principles defined in our strategy documents.

## File Organization

```
project-root/
├── docs/
│   ├── setup/
│   │   ├── setup-python.sh      # Python environment setup
│   │   ├── setup-node.sh        # Node.js environment setup
│   │   ├── setup-ruby.sh        # Ruby environment setup
│   │   └── setup-docker.sh      # Docker environment setup
│   ├── commands.md              # Project-specific commands
│   └── troubleshooting.md       # Common issues and solutions
├── docker-compose.yml           # Development environment
├── Dockerfile                   # Application container
├── .env.example                 # Environment variables template
└── README.md                    # Project overview
```

## Setup Script Templates

### Python Projects (`setup-python.sh`)
- Poetry/virtualenv/pipenv setup
- Dependency installation
- Environment activation
- Development tools configuration

### Node.js Projects (`setup-node.sh`)
- nvm configuration
- Node version management
- Package installation
- Development server setup

### Ruby Projects (`setup-ruby.sh`)
- rbenv configuration
- Ruby version management
- Gem installation
- Bundle setup

### Docker Projects (`setup-docker.sh`)
- Docker environment validation *(Environment Parity from `coding_principles.md`)*
- Container orchestration *(Docker setup from `ENVIRONMENT_SETUP.md`)*
- Development service startup
- Database initialization

## Configuration Templates

### Docker Compose (`docker-compose.yml`)
- Multi-service development environment *(Environment Parity strategy)*
- Database and cache services
- Volume mounts for hot reloading
- Network configuration
- Logging configuration *(Structured logging from `LOGGING_STRATEGY.md`)*

### Environment Variables (`.env.example`)
- Application configuration
- Database connection strings
- API keys and secrets (placeholder values)
- Feature flags
- Logging levels and configuration *(See `LOGGING_STRATEGY.md`)*

## Documentation Templates

### Commands Reference (`commands.md`)
- Development workflow commands
- Testing and debugging *(Test commands from `TESTING_STRATEGY.md`)*
- Deployment procedures
- Troubleshooting shortcuts

### Project README (`README.md`)
- Project overview and purpose
- Quick start guide
- Link to setup scripts
- Development workflow
- MCP server configuration notes *(If using servers from `MCP_STRATEGY.md`)*

## Implementation Strategy

**AI-Assisted Project Setup:**
With AI assistance, project template implementation happens in minutes. The human provides strategic direction while the AI handles the detailed implementation.

**Human Strategic Decisions:**
1. **Define Project Requirements:** Specify language stack, architecture, and key features
2. **Select Template Approach:** Choose appropriate templates based on project type
3. **Review Generated Files:** Validate AI-generated configurations and scripts
4. **Approve Implementation:** Ensure setup meets project needs and standards

**AI Implementation Tasks:**
- **Template Selection:** Choose and adapt relevant templates for the project
- **Script Generation:** Create customized setup scripts for the specific language stack
- **Configuration Creation:** Generate Docker, environment, and dependency files
- **Documentation Generation:** Create project-specific README and command references
- **Testing Setup:** Ensure all generated scripts work correctly

**Validation Process:**
- **Functionality Check:** Verify all setup scripts execute successfully
- **Environment Parity:** Confirm development environment matches production requirements
- **Documentation Review:** Ensure all generated documentation is accurate and complete
- **Security Validation:** Check that configurations follow security best practices

This approach leverages AI speed for implementation while maintaining human oversight for strategic decisions and quality assurance. 