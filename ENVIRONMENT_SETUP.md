# Project Environment Setup Strategy

This document outlines the principles and procedures for setting up project-specific development environments. For machine setup, refer to [`DEVELOPMENT_TOOLKIT.md`](DEVELOPMENT_TOOLKIT.md) and [`FOUNDATION_SETUP.md`](FOUNDATION_SETUP.md).

## Environment Strategy Philosophy

### 1. Environment Parity
**Principle:** Minimize differences between development, staging, and production environments to reduce deployment issues.

**Implementation:**
- Use identical runtime versions across all environments
- Maintain consistent configuration patterns
- Use containerization to ensure reproducible deployments
- Test migrations and deployments in staging before production

### 2. Dependency Isolation
**Principle:** Prevent dependency conflicts between projects and maintain clean development environments.

**Implementation:**
- Use virtual environments for all programming languages
- Maintain separate environments for different projects
- Use lock files to ensure consistent dependency versions
- Regularly clean and rebuild environments

### 3. Configuration as Code
**Principle:** All environment configuration should be version-controlled and reproducible.

**Implementation:**
- Store all configuration in version control
- Use environment variables for dynamic configuration
- Maintain environment-specific configuration files
- Document all configuration requirements

## Project Environment Components

### 1. Language Runtime Management

**Python Environment Setup:**
```bash
# Create project-specific Python environment
cd your-project
pyenv local 3.11.7  # Set Python version for project
python -m venv venv  # Create virtual environment
source venv/bin/activate  # Activate environment (Unix)
# venv\Scripts\activate  # Activate environment (Windows)

# Install dependencies
pip install -r requirements.txt

# Generate requirements with versions
pip freeze > requirements-lock.txt

# Alternative: Use Poetry for dependency management
poetry init  # Initialize poetry project
poetry add package-name  # Add dependencies
poetry install  # Install dependencies from pyproject.toml
poetry shell  # Activate virtual environment
```

**Node.js Environment Setup:**
```bash
# Set Node.js version for project
cd your-project
echo "18.19.0" > .nvmrc  # Specify Node version
nvm use  # Use Node version from .nvmrc

# Initialize project
npm init -y  # Create package.json
npm install package-name  # Install dependencies
npm install --save-dev dev-package  # Install dev dependencies

# Alternative: Use Yarn for faster installs
yarn init -y
yarn add package-name
yarn add --dev dev-package
```

**Ruby Environment Setup:**
```bash
# Set Ruby version for project
cd your-project
echo "3.2.0" > .ruby-version  # Specify Ruby version
rbenv local 3.2.0  # Set local Ruby version

# Initialize Bundler
bundle init  # Create Gemfile
bundle add gem-name  # Add gems
bundle install  # Install dependencies
```

### 2. Database Environment Setup

**PostgreSQL Development Setup:**
```bash
# Using Docker for consistent database environment
cat > docker-compose.yml << EOF
version: '3.8'
services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: \${DB_NAME:-myapp_development}
      POSTGRES_USER: \${DB_USER:-postgres}
      POSTGRES_PASSWORD: \${DB_PASSWORD:-password}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    restart: unless-stopped

volumes:
  postgres_data:
EOF

# Start database
docker-compose up -d postgres

# Connect to database
psql -h localhost -U postgres -d myapp_development
```

**Redis Development Setup:**
```bash
# Add Redis to docker-compose.yml
cat >> docker-compose.yml << EOF
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    restart: unless-stopped
EOF
```

### 3. Environment Configuration Management

**Environment Variables Setup:**
```bash
# Create .env.example template
cat > .env.example << EOF
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=myapp_development
DB_USER=postgres
DB_PASSWORD=password

# Redis Configuration
REDIS_URL=redis://localhost:6379/0

# Application Configuration
APP_ENV=development
APP_PORT=3000
APP_SECRET_KEY=your-secret-key-here

# External Services
API_KEY=your-api-key-here
SMTP_HOST=localhost
SMTP_PORT=1025

# Feature Flags
ENABLE_FEATURE_X=true
DEBUG_MODE=true
EOF

# Copy template for local development
cp .env.example .env.local

# Add .env files to .gitignore
echo ".env*" >> .gitignore
echo "!.env.example" >> .gitignore
```

**Configuration Loading (Python):**
```python
# config.py
import os
from pathlib import Path
from dotenv import load_dotenv

# Load environment variables from .env file
env_path = Path('.') / '.env.local'
load_dotenv(dotenv_path=env_path)

class Config:
    """Application configuration class."""
    
    # Database configuration
    DB_HOST = os.getenv('DB_HOST', 'localhost')
    DB_PORT = int(os.getenv('DB_PORT', 5432))
    DB_NAME = os.getenv('DB_NAME', 'myapp_development')
    DB_USER = os.getenv('DB_USER', 'postgres')
    DB_PASSWORD = os.getenv('DB_PASSWORD', 'password')
    
    # Application configuration
    APP_ENV = os.getenv('APP_ENV', 'development')
    APP_PORT = int(os.getenv('APP_PORT', 3000))
    SECRET_KEY = os.getenv('APP_SECRET_KEY', 'dev-secret-key')
    
    # Feature flags
    ENABLE_FEATURE_X = os.getenv('ENABLE_FEATURE_X', 'false').lower() == 'true'
    DEBUG_MODE = os.getenv('DEBUG_MODE', 'false').lower() == 'true'
    
    @property
    def database_url(self):
        """Construct database URL from components."""
        return f"postgresql://{self.DB_USER}:{self.DB_PASSWORD}@{self.DB_HOST}:{self.DB_PORT}/{self.DB_NAME}"

# Usage
config = Config()
```

**Configuration Loading (Node.js):**
```javascript
// config.js
const path = require('path');
require('dotenv').config({ path: path.join(__dirname, '.env.local') });

const config = {
  // Database configuration
  database: {
    host: process.env.DB_HOST || 'localhost',
    port: parseInt(process.env.DB_PORT) || 5432,
    name: process.env.DB_NAME || 'myapp_development',
    user: process.env.DB_USER || 'postgres',
    password: process.env.DB_PASSWORD || 'password',
    
    get url() {
      return `postgresql://${this.user}:${this.password}@${this.host}:${this.port}/${this.name}`;
    }
  },
  
  // Application configuration
  app: {
    env: process.env.APP_ENV || 'development',
    port: parseInt(process.env.APP_PORT) || 3000,
    secretKey: process.env.APP_SECRET_KEY || 'dev-secret-key'
  },
  
  // Feature flags
  features: {
    enableFeatureX: process.env.ENABLE_FEATURE_X === 'true',
    debugMode: process.env.DEBUG_MODE === 'true'
  }
};

module.exports = config;
```

### 4. Development Services Setup

**Complete Development Stack:**
```yaml
# docker-compose.yml
version: '3.8'

services:
  # Application
  app:
    build: .
    ports:
      - "${APP_PORT:-3000}:3000"
    environment:
      - NODE_ENV=development
      - DB_HOST=postgres
      - REDIS_URL=redis://redis:6379/0
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      - postgres
      - redis
    restart: unless-stopped

  # Database
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ${DB_NAME:-myapp_development}
      POSTGRES_USER: ${DB_USER:-postgres}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-password}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./database/init:/docker-entrypoint-initdb.d
    restart: unless-stopped

  # Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    restart: unless-stopped

  # Email testing
  mailhog:
    image: mailhog/mailhog:latest
    ports:
      - "1025:1025"  # SMTP
      - "8025:8025"  # Web UI
    restart: unless-stopped

volumes:
  postgres_data:
```

## Environment Testing and Validation

### 1. Environment Health Checks

**Health Check Script:**
```bash
#!/bin/bash
# check-environment.sh

echo "🔍 Environment Health Check"
echo "=========================="

# Check if virtual environment is active
if [[ "$VIRTUAL_ENV" != "" ]]; then
    echo "✓ Python virtual environment active: $VIRTUAL_ENV"
else
    echo "⚠ Python virtual environment not active"
fi

# Check Node.js version
if command -v node &> /dev/null; then
    node_version=$(node --version)
    echo "✓ Node.js version: $node_version"
    
    # Check if using correct version
    if [ -f ".nvmrc" ]; then
        expected_version=$(cat .nvmrc)
        if [[ "$node_version" == *"$expected_version"* ]]; then
            echo "✓ Using correct Node.js version"
        else
            echo "⚠ Node.js version mismatch. Expected: $expected_version, Got: $node_version"
        fi
    fi
fi

# Check database connectivity
echo "📊 Checking database connectivity..."
if command -v psql &> /dev/null; then
    if psql -h localhost -U postgres -d myapp_development -c "SELECT 1;" &> /dev/null; then
        echo "✓ PostgreSQL connection successful"
    else
        echo "✗ PostgreSQL connection failed"
    fi
fi

# Check Redis connectivity
if command -v redis-cli &> /dev/null; then
    if redis-cli ping &> /dev/null; then
        echo "✓ Redis connection successful"
    else
        echo "✗ Redis connection failed"
    fi
fi

# Check environment variables
echo "🔧 Checking environment configuration..."
if [ -f ".env.local" ]; then
    echo "✓ .env.local file found"
else
    echo "⚠ .env.local file not found"
fi

echo "=========================="
echo "Environment check complete"
```

### 2. Dependency Verification

**Dependency Check Script:**
```bash
#!/bin/bash
# check-dependencies.sh

echo "📦 Dependency Check"
echo "==================="

# Check Python dependencies
if [ -f "requirements.txt" ]; then
    echo "Checking Python dependencies..."
    pip check
    if [ $? -eq 0 ]; then
        echo "✓ Python dependencies OK"
    else
        echo "⚠ Python dependency conflicts detected"
    fi
fi

# Check Node.js dependencies
if [ -f "package.json" ]; then
    echo "Checking Node.js dependencies..."
    npm audit --audit-level moderate
    if [ $? -eq 0 ]; then
        echo "✓ Node.js dependencies OK"
    else
        echo "⚠ Node.js security vulnerabilities detected"
    fi
fi

echo "==================="
echo "Dependency check complete"
```

## Project-Specific Setup Procedures

### 1. New Project Environment Setup

**Project Initialization Script:**
```bash
#!/bin/bash
# init-project-env.sh

PROJECT_NAME=$1
if [ -z "$PROJECT_NAME" ]; then
    echo "Usage: $0 <project-name>"
    exit 1
fi

echo "🚀 Initializing environment for: $PROJECT_NAME"

# Create project directory
mkdir -p "$PROJECT_NAME"
cd "$PROJECT_NAME"

# Initialize Git
git init
echo "node_modules/" > .gitignore
echo ".env*" >> .gitignore
echo "!.env.example" >> .gitignore

# Create basic structure
mkdir -p src tests docs scripts
mkdir -p src/{components,services,utils}
mkdir -p tests/{unit,integration}

# Initialize Node.js project
npm init -y
npm install --save-dev nodemon jest

# Create environment template
cat > .env.example << EOF
NODE_ENV=development
PORT=3000
DB_HOST=localhost
DB_PORT=5432
DB_NAME=${PROJECT_NAME}_development
EOF

# Copy environment template
cp .env.example .env.local

# Create basic package.json scripts
npm pkg set scripts.dev="nodemon src/index.js"
npm pkg set scripts.test="jest"
npm pkg set scripts.start="node src/index.js"

echo "✓ Project environment initialized"
echo "Next steps:"
echo "1. Edit .env.local with your configuration"
echo "2. Start development with: npm run dev"
```

### 2. Environment Migration and Updates

**Environment Update Script:**
```bash
#!/bin/bash
# update-environment.sh

echo "🔄 Updating development environment"

# Update dependencies
if [ -f "requirements.txt" ]; then
    echo "Updating Python dependencies..."
    pip install --upgrade pip
    pip install --upgrade -r requirements.txt
fi

if [ -f "package.json" ]; then
    echo "Updating Node.js dependencies..."
    npm update
fi

# Update Docker images
if [ -f "docker-compose.yml" ]; then
    echo "Updating Docker images..."
    docker-compose pull
fi

# Restart services
docker-compose down
docker-compose up -d

echo "✓ Environment update complete"
```

## Troubleshooting Common Issues

### 1. Virtual Environment Issues

**Problem:** Virtual environment not activating
**Solution:**
```bash
# Recreate virtual environment
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 2. Port Conflicts

**Problem:** Port already in use
**Solution:**
```bash
# Find process using port
lsof -i :3000

# Kill process if needed
kill $(lsof -t -i:3000)

# Use different port
export PORT=3001
```

### 3. Database Connection Issues

**Problem:** Cannot connect to database
**Solution:**
```bash
# Restart database container
docker-compose restart postgres

# Check database logs
docker-compose logs postgres

# Reset database
docker-compose down -v
docker-compose up -d postgres
```

## Best Practices for AI Assistants

### 1. Environment Documentation
When setting up environments, always:
- Document all configuration requirements
- Provide working examples in .env.example
- Include setup and troubleshooting scripts
- Maintain clear README instructions

### 2. Environment Validation
Before beginning development:
- Run environment health checks
- Verify all services are running
- Test database connectivity
- Confirm all dependencies are installed

### 3. Environment Maintenance
Regularly:
- Update dependencies to latest stable versions
- Audit for security vulnerabilities
- Clean up unused containers and volumes
- Review and update configuration

For additional environment guidance, refer to [`FOUNDATION_SETUP.md`](FOUNDATION_SETUP.md) for machine setup and [`DEPLOYMENT_STRATEGY.md`](DEPLOYMENT_STRATEGY.md) for production environments.