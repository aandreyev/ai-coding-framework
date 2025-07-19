# Code Quality Strategy

This document defines code quality standards for AI assistants. Follow these practices to write maintainable, readable, and robust code.

## Core Quality Principles

### 1. Write Self-Documenting Code
**Rule:** Code should be readable and understandable without external documentation.

```python
# DO: Clear, descriptive names
def calculate_monthly_subscription_fee(user_tier, discount_percentage):
    base_fee = get_base_fee_for_tier(user_tier)
    discount_amount = base_fee * (discount_percentage / 100)
    return base_fee - discount_amount

# DON'T: Unclear names and logic
def calc(t, d):
    x = get_fee(t)
    y = x * d / 100
    return x - y
```

**Implementation Guidelines:**
- Use descriptive variable and function names
- Avoid abbreviations unless universally understood
- Make intent clear through naming
- Use consistent naming conventions

### 2. Keep Functions Small and Focused
**Rule:** Functions should do one thing well (Single Responsibility Principle).

```python
# DO: Small, focused functions
def validate_email(email):
    """Validate email format and length."""
    if not email or len(email) > 254:
        return False
    return re.match(r'^[^@]+@[^@]+\.[^@]+$', email) is not None

def check_email_domain_allowed(email):
    """Check if email domain is in allowed list."""
    domain = email.split('@')[1]
    return domain in ALLOWED_DOMAINS

def process_user_registration(email, password):
    """Process user registration with validation."""
    if not validate_email(email):
        raise ValueError("Invalid email format")
    
    if not check_email_domain_allowed(email):
        raise ValueError("Email domain not allowed")
    
    return create_user(email, hash_password(password))

# DON'T: Large function doing multiple things
def process_user_registration(email, password):
    # Email validation
    if not email or len(email) > 254:
        raise ValueError("Invalid email")
    if not re.match(r'^[^@]+@[^@]+\.[^@]+$', email):
        raise ValueError("Invalid email format")
    
    # Domain checking
    domain = email.split('@')[1]
    if domain not in ALLOWED_DOMAINS:
        raise ValueError("Domain not allowed")
    
    # Password hashing
    salt = bcrypt.gensalt()
    hashed_password = bcrypt.hashpw(password.encode('utf-8'), salt)
    
    # Database operations
    # ... 50+ more lines
```

**Guidelines:**
- Keep functions under 50 lines
- Each function should have a single responsibility
- Extract complex logic into separate functions
- Use descriptive function names that indicate purpose

### 3. Handle Errors Gracefully
**Rule:** Anticipate and handle potential failures appropriately.

```python
# DO: Proper error handling
def get_user_profile(user_id):
    """Fetch user profile with proper error handling."""
    try:
        user = User.objects.get(id=user_id)
        return {
            'id': user.id,
            'name': user.name,
            'email': user.email
        }
    except User.DoesNotExist:
        logger.warning(f"User {user_id} not found")
        raise UserNotFoundError(f"User {user_id} does not exist")
    except DatabaseError as e:
        logger.error(f"Database error fetching user {user_id}: {e}")
        raise ServiceUnavailableError("Unable to fetch user data")

# DON'T: Ignore errors or use bare except
def get_user_profile(user_id):
    try:
        user = User.objects.get(id=user_id)
        return user.name, user.email
    except:  # Never use bare except
        return None  # Swallowing errors
```

**Error Handling Checklist:**
- [ ] Use specific exception types
- [ ] Log errors with context
- [ ] Provide meaningful error messages
- [ ] Don't swallow exceptions silently
- [ ] Follow error handling patterns from [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)

### 4. Write Comprehensive Tests
**Rule:** Test all code paths, edge cases, and error conditions.

```python
# DO: Comprehensive test coverage
class TestEmailValidator:
    def test_valid_email_returns_true(self):
        assert validate_email("user@example.com") is True
    
    def test_email_without_at_returns_false(self):
        assert validate_email("userexample.com") is False
    
    def test_email_without_domain_returns_false(self):
        assert validate_email("user@") is False
    
    def test_empty_email_returns_false(self):
        assert validate_email("") is False
    
    def test_none_email_returns_false(self):
        assert validate_email(None) is False
    
    def test_email_too_long_returns_false(self):
        long_email = "a" * 250 + "@example.com"
        assert validate_email(long_email) is False

# DON'T: Minimal testing
def test_email():
    assert validate_email("user@example.com")  # Only happy path
```

**Testing Requirements:**
- [ ] Test happy path scenarios
- [ ] Test edge cases and boundary conditions
- [ ] Test error conditions and exceptions
- [ ] Achieve minimum 80% code coverage
- [ ] Follow testing patterns from [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md)

## Code Organization Standards

### 1. File and Directory Structure
```
project/
├── src/
│   ├── models/           # Data models
│   ├── services/         # Business logic
│   ├── controllers/      # Request handlers
│   ├── utils/           # Utility functions
│   └── config/          # Configuration
├── tests/
│   ├── unit/            # Unit tests
│   ├── integration/     # Integration tests
│   └── fixtures/        # Test data
└── docs/
    ├── api/             # API documentation
    └── architecture/    # System design docs
```

### 2. Import Organization
```python
# DO: Organized imports
# Standard library imports
import os
import sys
from datetime import datetime

# Third-party imports
import requests
from flask import Flask, request
from sqlalchemy import create_engine

# Local application imports
from models.user import User
from services.email_service import EmailService
from utils.validators import validate_email

# DON'T: Disorganized imports
from flask import Flask
import os
from models.user import User
import requests
from utils.validators import validate_email
import sys
```

### 3. Documentation Standards

```python
def calculate_compound_interest(principal, rate, time, compound_frequency=1):
    """
    Calculate compound interest for an investment.
    
    Args:
        principal (float): Initial investment amount
        rate (float): Annual interest rate (as decimal, e.g., 0.05 for 5%)
        time (int): Investment period in years
        compound_frequency (int, optional): Compounding frequency per year. Defaults to 1.
    
    Returns:
        float: Final amount after compound interest
    
    Raises:
        ValueError: If principal, rate, or time is negative
        TypeError: If inputs are not numeric
    
    Example:
        >>> calculate_compound_interest(1000, 0.05, 10, 4)
        1643.62
    """
    if principal < 0 or rate < 0 or time < 0:
        raise ValueError("Principal, rate, and time must be non-negative")
    
    return principal * (1 + rate / compound_frequency) ** (compound_frequency * time)
```

## Code Style Guidelines

### 1. Python Style (PEP 8)
```python
# DO: Follow PEP 8
class UserService:
    """Service for managing user operations."""
    
    MAX_LOGIN_ATTEMPTS = 3
    
    def __init__(self, database_connection):
        self.db = database_connection
        self._failed_attempts = {}
    
    def authenticate_user(self, email, password):
        """Authenticate user with email and password."""
        if self._is_account_locked(email):
            raise AccountLockedError("Account is temporarily locked")
        
        user = self._get_user_by_email(email)
        if not user or not self._verify_password(password, user.password_hash):
            self._record_failed_attempt(email)
            raise AuthenticationError("Invalid credentials")
        
        self._reset_failed_attempts(email)
        return user

# DON'T: Inconsistent style
class userservice:
    max_login_attempts=3
    def __init__(self,db):
        self.db=db
        self.failed_attempts={}
    def authenticateUser(self,email,password):
        if self.isAccountLocked(email):raise AccountLockedError("locked")
        # ... rest of implementation
```

### 2. JavaScript/TypeScript Style
```typescript
// DO: Clear, consistent TypeScript
interface UserProfile {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
}

class UserService {
  private readonly apiClient: ApiClient;
  
  constructor(apiClient: ApiClient) {
    this.apiClient = apiClient;
  }
  
  async getUserProfile(userId: string): Promise<UserProfile> {
    try {
      const response = await this.apiClient.get(`/users/${userId}`);
      return response.data;
    } catch (error) {
      if (error.status === 404) {
        throw new UserNotFoundError(`User ${userId} not found`);
      }
      throw new ServiceError('Failed to fetch user profile');
    }
  }
}

// DON'T: Inconsistent, unclear code
class userservice {
  constructor(api) { this.api = api; }
  getUser(id) {
    return this.api.get('/users/' + id).then(r => r.data).catch(e => null);
  }
}
```

## Code Review Standards

### 1. Code Review Checklist
**Functionality:**
- [ ] Code implements requirements correctly
- [ ] Edge cases are handled appropriately
- [ ] Error conditions are handled properly
- [ ] Performance considerations are addressed

**Code Quality:**
- [ ] Code is readable and well-structured
- [ ] Functions are small and focused
- [ ] Variable and function names are descriptive
- [ ] Code follows established patterns

**Testing:**
- [ ] Tests cover new functionality
- [ ] Tests include edge cases and error conditions
- [ ] Test names are descriptive
- [ ] Tests are maintainable and fast

**Security:**
- [ ] Input validation is implemented
- [ ] Security best practices are followed
- [ ] No sensitive data is exposed
- [ ] Authentication/authorization is correct

### 2. Review Process
1. **Self-Review:** Author reviews their own code before submission
2. **Automated Checks:** CI/CD pipeline runs tests and linting
3. **Peer Review:** At least one team member reviews the code
4. **Address Feedback:** Author responds to and resolves feedback
5. **Final Approval:** Code is approved and merged

## Quality Metrics and Monitoring

### 1. Code Quality Metrics
- **Code Coverage:** Maintain >80% test coverage
- **Complexity:** Keep cyclomatic complexity <10 per function
- **Duplication:** Minimize code duplication (<5%)
- **Technical Debt:** Track and address technical debt regularly

### 2. Automated Quality Checks
```yaml
# Example CI/CD quality gates
quality_gates:
  - name: "Unit Tests"
    command: "pytest --cov=src --cov-report=xml --cov-fail-under=80"
    
  - name: "Code Style"
    command: "flake8 src/ --max-line-length=88 --max-complexity=10"
    
  - name: "Type Checking"
    command: "mypy src/ --strict"
    
  - name: "Security Scan"
    command: "bandit -r src/ -f json"
    
  - name: "Dependency Check"
    command: "safety check --json"
```

## Refactoring Guidelines

### 1. When to Refactor
- Code is difficult to understand or modify
- Functions are too long (>50 lines)
- Code is duplicated in multiple places
- Tests are difficult to write or maintain
- Performance issues are identified

### 2. Refactoring Process
1. **Ensure Test Coverage:** Write tests before refactoring
2. **Make Small Changes:** Refactor in small, incremental steps
3. **Run Tests Frequently:** Ensure tests pass after each change
4. **Review and Validate:** Code review refactored code
5. **Monitor Performance:** Ensure refactoring doesn't degrade performance

## AI Assistant Guidelines

### 1. Code Generation Standards
When generating code, always:
- [ ] Use descriptive names for variables, functions, and classes
- [ ] Include proper error handling
- [ ] Add comprehensive docstrings
- [ ] Write accompanying unit tests
- [ ] Follow established code style guidelines
- [ ] Consider security implications
- [ ] Optimize for readability over cleverness

### 2. Code Review as AI
When reviewing code:
- [ ] Check for proper error handling
- [ ] Verify test coverage is adequate
- [ ] Ensure security best practices are followed
- [ ] Confirm code follows style guidelines
- [ ] Look for potential performance issues
- [ ] Suggest improvements for readability
- [ ] Validate that documentation is complete

## Quick Reference

**Functions:** <50 lines, single responsibility, descriptive names
**Testing:** >80% coverage, test happy path + edge cases + errors
**Documentation:** Docstrings for all public functions, clear inline comments
**Style:** Follow language-specific conventions (PEP 8, etc.)
**Security:** Validate inputs, handle errors, follow security guidelines
**Review:** Self-review → automated checks → peer review → approval

For detailed examples, see [`QUICK_REFERENCE_CARDS.md`](QUICK_REFERENCE_CARDS.md). 