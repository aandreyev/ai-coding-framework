# Security Strategy

This document provides security guidance for AI assistants working on development tasks. Follow these practices to build secure applications.

## Core Security Principles

### 1. Input Validation
**Rule:** Validate all inputs at system boundaries before processing.

```python
# DO: Validate inputs
def process_user_input(user_data):
    if not isinstance(user_data, str) or len(user_data) > 1000:
        raise ValueError("Invalid input format or length")
    
    sanitized = escape_html(user_data.strip())
    return sanitized

# DON'T: Process raw inputs
def process_user_input(user_data):
    return user_data  # Dangerous!
```

**Implementation Checklist:**
- [ ] Validate data type, length, format, and range
- [ ] Sanitize inputs before processing
- [ ] Use allow-lists over deny-lists when possible
- [ ] Reject invalid inputs with clear error messages

### 2. Authentication & Authorization
**Rule:** Implement proper identity verification and access control.

```python
# DO: Check authentication and authorization
@require_authentication
@require_permission("user:read")
def get_user_profile(user_id):
    if current_user.id != user_id and not current_user.is_admin:
        raise PermissionError("Access denied")
    return User.get(user_id)

# DON'T: Skip authorization checks
def get_user_profile(user_id):
    return User.get(user_id)  # Anyone can access any user!
```

**Implementation Checklist:**
- [ ] Authenticate users before granting access
- [ ] Implement role-based access control (RBAC)
- [ ] Use strong password policies
- [ ] Implement session management with timeouts
- [ ] Log authentication events

### 3. Data Protection
**Rule:** Protect sensitive data in transit and at rest.

```python
# DO: Encrypt sensitive data
import bcrypt
from cryptography.fernet import Fernet

def hash_password(password):
    return bcrypt.hashpw(password.encode(), bcrypt.gensalt())

def encrypt_data(data, key):
    cipher = Fernet(key)
    return cipher.encrypt(data.encode())

# DON'T: Store passwords in plaintext
def store_password(password):
    user.password = password  # Never do this!
```

**Implementation Checklist:**
- [ ] Use HTTPS for all data transmission
- [ ] Hash passwords with salt using bcrypt/scrypt
- [ ] Encrypt sensitive data at rest
- [ ] Implement secure key management
- [ ] Never log sensitive information

### 4. SQL Injection Prevention
**Rule:** Use parameterized queries for all database interactions.

```python
# DO: Use parameterized queries
def get_user_by_email(email):
    query = "SELECT * FROM users WHERE email = %s"
    return db.execute(query, (email,))

# DON'T: String concatenation
def get_user_by_email(email):
    query = f"SELECT * FROM users WHERE email = '{email}'"
    return db.execute(query)  # SQL injection risk!
```

**Implementation Checklist:**
- [ ] Use ORM or parameterized queries exclusively
- [ ] Validate database inputs
- [ ] Implement least privilege database access
- [ ] Monitor and log database access patterns

### 5. Cross-Site Scripting (XSS) Prevention
**Rule:** Escape all user-generated content in web applications.

```javascript
// DO: Escape user content
function displayUserComment(comment) {
    const escaped = escapeHtml(comment);
    document.getElementById('comments').textContent = escaped;
}

// DON'T: Insert raw user content
function displayUserComment(comment) {
    document.getElementById('comments').innerHTML = comment; // XSS risk!
}
```

**Implementation Checklist:**
- [ ] Escape HTML content by default
- [ ] Use Content Security Policy (CSP) headers
- [ ] Validate and sanitize rich text content
- [ ] Use framework-provided escaping functions

## Security Implementation Patterns

### Secure API Design
```python
from functools import wraps
import jwt

def require_api_key(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        api_key = request.headers.get('X-API-Key')
        if not validate_api_key(api_key):
            return {'error': 'Invalid API key'}, 401
        return f(*args, **kwargs)
    return decorated_function

@app.route('/api/users', methods=['GET'])
@require_api_key
@rate_limit('100/hour')
def get_users():
    return jsonify(users)
```

### Error Handling Security
```python
# DO: Generic error messages
try:
    user = authenticate(username, password)
except AuthenticationError:
    return {"error": "Invalid credentials"}, 401

# DON'T: Detailed error messages
try:
    user = authenticate(username, password)
except UserNotFoundError:
    return {"error": "User does not exist"}, 401  # Information disclosure
except InvalidPasswordError:
    return {"error": "Wrong password"}, 401  # Information disclosure
```

### Secure Configuration
```python
# DO: Environment-based configuration
import os

DATABASE_URL = os.getenv('DATABASE_URL')
SECRET_KEY = os.getenv('SECRET_KEY')
DEBUG = os.getenv('DEBUG', 'False').lower() == 'true'

# DON'T: Hardcoded secrets
DATABASE_URL = "postgresql://user:password@localhost/db"  # Never!
SECRET_KEY = "hardcoded-secret-key"  # Never!
```

## Security Checklist for AI Assistants

When implementing any feature, verify:

### Input Security
- [ ] All inputs validated before processing
- [ ] File uploads scanned and restricted
- [ ] URL parameters sanitized
- [ ] Form data escaped and validated

### Authentication Security
- [ ] Authentication required for protected resources
- [ ] Session management implemented correctly
- [ ] Password policies enforced
- [ ] Multi-factor authentication considered

### Data Security
- [ ] Sensitive data encrypted at rest
- [ ] HTTPS used for all communications
- [ ] Database queries parameterized
- [ ] Logs free of sensitive information

### Application Security
- [ ] Dependencies updated and vulnerability-free
- [ ] Security headers implemented
- [ ] Error messages don't reveal system details
- [ ] Rate limiting implemented for APIs

### Infrastructure Security
- [ ] Environment variables used for secrets
- [ ] Least privilege access implemented
- [ ] Security monitoring and alerting configured
- [ ] Backup and recovery procedures tested

## Common Vulnerabilities to Avoid

### 1. Injection Attacks
- SQL injection via string concatenation
- Command injection via unsanitized system calls
- LDAP injection in directory queries

### 2. Broken Authentication
- Weak password policies
- Session fixation vulnerabilities
- Missing logout functionality

### 3. Sensitive Data Exposure
- Unencrypted data transmission
- Plaintext password storage
- Excessive data in error messages

### 4. Broken Access Control
- Missing authorization checks
- Insecure direct object references
- Privilege escalation vulnerabilities

### 5. Security Misconfiguration
- Default credentials in production
- Unnecessary services enabled
- Missing security headers

## Security Testing

### Manual Testing
- [ ] Test authentication bypass attempts
- [ ] Verify authorization enforcement
- [ ] Check input validation edge cases
- [ ] Review error message content

### Automated Testing
- [ ] Run dependency vulnerability scans
- [ ] Implement security unit tests
- [ ] Use static code analysis tools
- [ ] Perform automated penetration testing

## Incident Response

### When Security Issues Are Discovered
1. **Assess impact** and affected systems
2. **Document** the vulnerability details
3. **Implement** immediate mitigation
4. **Notify** stakeholders and users if needed
5. **Update** code and security measures
6. **Review** and improve security processes

### Security Logging
Log these security events:
- Authentication attempts (success/failure)
- Authorization failures
- Input validation failures
- Administrative actions
- Data access patterns

## Quick Reference

**Always validate inputs** → Use type checking, length limits, format validation
**Always authenticate** → Require valid credentials before access
**Always authorize** → Check permissions for specific actions
**Always encrypt** → Use HTTPS, hash passwords, encrypt sensitive data
**Always log** → Track security events for monitoring and incident response

For detailed implementation examples, refer to [`QUICK_REFERENCE_CARDS.md`](QUICK_REFERENCE_CARDS.md). 