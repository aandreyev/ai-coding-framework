# Error Resilience Strategy

This document provides error handling guidance for AI assistants to build robust, fault-tolerant applications. Follow these patterns to create systems that gracefully handle failures and provide excellent user experiences.

## Core Error Handling Principles

### 1. Fail Fast, Fail Safe
**Rule:** Detect errors early and handle them gracefully without exposing system internals.

```python
# DO: Validate inputs early and fail fast
def process_payment(amount, payment_method):
    """Process payment with comprehensive validation."""
    # Validate inputs immediately
    if not isinstance(amount, (int, float)) or amount <= 0:
        raise ValueError("Amount must be a positive number")
    
    if payment_method not in ['credit_card', 'paypal', 'bank_transfer']:
        raise ValueError("Invalid payment method")
    
    try:
        result = payment_gateway.charge(amount, payment_method)
        return {"success": True, "transaction_id": result.id}
    except PaymentGatewayError as e:
        logger.error(f"Payment failed: {e}", extra={"amount": amount, "method": payment_method})
        raise PaymentProcessingError("Payment could not be processed. Please try again.")
    except NetworkError as e:
        logger.error(f"Network error during payment: {e}")
        raise ServiceUnavailableError("Payment service temporarily unavailable")

# DON'T: Let errors propagate with system details
def process_payment(amount, payment_method):
    result = payment_gateway.charge(amount, payment_method)  # No validation
    return result  # Exposes internal errors
```

### 2. Comprehensive Error Classification
**Rule:** Categorize errors to enable appropriate handling strategies.

```python
# Error hierarchy for clear handling
class ApplicationError(Exception):
    """Base application error."""
    pass

class ValidationError(ApplicationError):
    """Input validation failed."""
    pass

class BusinessLogicError(ApplicationError):
    """Business rule violation."""
    pass

class ExternalServiceError(ApplicationError):
    """External service failure."""
    pass

class DatabaseError(ApplicationError):
    """Database operation failed."""
    pass

# Usage with specific handling
def create_user_account(email, password):
    try:
        if not validate_email(email):
            raise ValidationError("Invalid email format")
        
        if User.objects.filter(email=email).exists():
            raise BusinessLogicError("Account already exists")
        
        user = User.objects.create(email=email, password=hash_password(password))
        return user
        
    except ValidationError as e:
        return {"error": str(e), "code": "VALIDATION_ERROR"}
    except BusinessLogicError as e:
        return {"error": str(e), "code": "BUSINESS_ERROR"}
    except DatabaseError as e:
        logger.error(f"Database error creating user: {e}")
        return {"error": "Account creation failed", "code": "SERVICE_ERROR"}
```

### 3. User-Friendly Error Messages
**Rule:** Provide helpful error messages that guide users toward resolution.

```javascript
// DO: Provide actionable error messages
const ErrorMessages = {
  VALIDATION_EMAIL: {
    message: "Please enter a valid email address",
    action: "Check your email format (example@domain.com)"
  },
  VALIDATION_PASSWORD: {
    message: "Password must be at least 8 characters with one number",
    action: "Create a stronger password"
  },
  NETWORK_TIMEOUT: {
    message: "Request timed out",
    action: "Please check your internet connection and try again"
  },
  SERVER_ERROR: {
    message: "Something went wrong on our end",
    action: "Please try again in a few minutes"
  }
};

function handleApiError(error) {
  const errorInfo = ErrorMessages[error.code] || ErrorMessages.SERVER_ERROR;
  
  return {
    message: errorInfo.message,
    action: errorInfo.action,
    code: error.code,
    timestamp: new Date().toISOString()
  };
}

// DON'T: Expose technical details
function handleApiError(error) {
  return {
    message: error.stack,  // Never expose stack traces
    details: error.sqlQuery  // Never expose internal details
  };
}
```

## Error Handling Patterns

### 1. Retry with Exponential Backoff
**Pattern:** Automatically retry failed operations with increasing delays.

```python
import time
import random
from functools import wraps

def retry_with_backoff(max_retries=3, base_delay=1, max_delay=60):
    """Decorator for retrying functions with exponential backoff."""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries + 1):
                try:
                    return func(*args, **kwargs)
                except (ConnectionError, TimeoutError) as e:
                    if attempt == max_retries:
                        raise e
                    
                    # Calculate delay with jitter
                    delay = min(base_delay * (2 ** attempt), max_delay)
                    jitter = random.uniform(0, 0.1) * delay
                    sleep_time = delay + jitter
                    
                    logger.warning(f"Attempt {attempt + 1} failed, retrying in {sleep_time:.2f}s: {e}")
                    time.sleep(sleep_time)
            
            return func(*args, **kwargs)
        return wrapper
    return decorator

# Usage
@retry_with_backoff(max_retries=3, base_delay=1)
def call_external_api(endpoint, data):
    """Call external API with automatic retry."""
    response = requests.post(endpoint, json=data, timeout=30)
    response.raise_for_status()
    return response.json()
```

### 2. Circuit Breaker Pattern
**Pattern:** Temporarily disable failing services to prevent cascade failures.

```python
import time
from enum import Enum

class CircuitState(Enum):
    CLOSED = "closed"      # Normal operation
    OPEN = "open"          # Service failing, reject requests
    HALF_OPEN = "half_open"  # Testing if service recovered

class CircuitBreaker:
    def __init__(self, failure_threshold=5, recovery_timeout=60):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.failure_count = 0
        self.last_failure_time = None
        self.state = CircuitState.CLOSED
    
    def call(self, func, *args, **kwargs):
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                self.state = CircuitState.HALF_OPEN
            else:
                raise ServiceUnavailableError("Service temporarily unavailable")
        
        try:
            result = func(*args, **kwargs)
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise e
    
    def on_success(self):
        self.failure_count = 0
        self.state = CircuitState.CLOSED
    
    def on_failure(self):
        self.failure_count += 1
        self.last_failure_time = time.time()
        
        if self.failure_count >= self.failure_threshold:
            self.state = CircuitState.OPEN

# Usage
payment_circuit = CircuitBreaker(failure_threshold=3, recovery_timeout=30)

def process_payment_safely(amount, method):
    try:
        return payment_circuit.call(payment_gateway.charge, amount, method)
    except ServiceUnavailableError:
        return {"error": "Payment service temporarily unavailable", "retry_after": 30}
```

### 3. Graceful Degradation
**Pattern:** Provide reduced functionality when systems fail rather than complete failure.

```python
def get_user_recommendations(user_id):
    """Get user recommendations with fallback strategies."""
    try:
        # Try personalized recommendations
        return ml_service.get_personalized_recommendations(user_id)
    except MLServiceError:
        logger.warning(f"ML service failed for user {user_id}, using popular items")
        try:
            # Fallback to popular items
            return cache.get_popular_items()
        except CacheError:
            logger.warning("Cache failed, using default recommendations")
            # Final fallback to static recommendations
            return get_default_recommendations()

def get_default_recommendations():
    """Static fallback recommendations."""
    return [
        {"id": 1, "title": "Featured Product 1", "type": "featured"},
        {"id": 2, "title": "Featured Product 2", "type": "featured"},
        {"id": 3, "title": "Popular Product", "type": "popular"}
    ]
```

## Error Monitoring and Alerting

### 1. Structured Error Logging
**Pattern:** Log errors with context for effective debugging.

```python
import logging
import json
from datetime import datetime

def setup_error_logging():
    """Configure structured error logging."""
    logging.basicConfig(
        level=logging.INFO,
        format='%(message)s',
        handlers=[logging.StreamHandler()]
    )

def log_error(error, context=None):
    """Log error with structured context."""
    error_data = {
        "timestamp": datetime.utcnow().isoformat(),
        "error_type": type(error).__name__,
        "error_message": str(error),
        "context": context or {},
        "severity": "error"
    }
    
    logging.error(json.dumps(error_data))

# Usage in error handling
def process_order(order_data):
    try:
        # Process order logic
        return create_order(order_data)
    except ValidationError as e:
        log_error(e, {
            "order_id": order_data.get("id"),
            "user_id": order_data.get("user_id"),
            "operation": "order_creation"
        })
        raise e
```

### 2. Error Rate Monitoring
**Pattern:** Track error rates and patterns for proactive issue detection.

```python
class ErrorMetrics:
    def __init__(self):
        self.error_counts = {}
        self.total_requests = 0
    
    def record_error(self, error_type, endpoint=None):
        """Record error occurrence."""
        key = f"{error_type}:{endpoint}" if endpoint else error_type
        self.error_counts[key] = self.error_counts.get(key, 0) + 1
    
    def record_request(self):
        """Record total request count."""
        self.total_requests += 1
    
    def get_error_rate(self, error_type=None):
        """Calculate error rate."""
        if error_type:
            error_count = sum(count for key, count in self.error_counts.items() 
                            if key.startswith(error_type))
        else:
            error_count = sum(self.error_counts.values())
        
        return error_count / max(self.total_requests, 1)

# Usage
metrics = ErrorMetrics()

def api_endpoint_wrapper(func):
    """Decorator to track API metrics."""
    @wraps(func)
    def wrapper(*args, **kwargs):
        metrics.record_request()
        try:
            return func(*args, **kwargs)
        except Exception as e:
            metrics.record_error(type(e).__name__, func.__name__)
            raise e
    return wrapper
```

## Testing Error Scenarios

### 1. Error Injection Testing
**Pattern:** Systematically test error handling by injecting failures.

```python
import pytest
from unittest.mock import patch, Mock

class TestErrorHandling:
    def test_payment_network_error(self):
        """Test payment handling when network fails."""
        with patch('payment_gateway.charge') as mock_charge:
            mock_charge.side_effect = NetworkError("Connection timeout")
            
            result = process_payment(100.0, 'credit_card')
            
            assert result["error"] == "Payment service temporarily unavailable"
            assert "transaction_id" not in result
    
    def test_database_error_fallback(self):
        """Test fallback when database is unavailable."""
        with patch('User.objects.create') as mock_create:
            mock_create.side_effect = DatabaseError("Connection lost")
            
            result = create_user_account("test@example.com", "password123")
            
            assert result["code"] == "SERVICE_ERROR"
            assert "Account creation failed" in result["error"]
    
    def test_circuit_breaker_opens(self):
        """Test circuit breaker opens after repeated failures."""
        circuit = CircuitBreaker(failure_threshold=2)
        
        # Cause failures to open circuit
        for _ in range(2):
            with pytest.raises(Exception):
                circuit.call(lambda: 1/0)  # Division by zero
        
        # Circuit should now be open
        with pytest.raises(ServiceUnavailableError):
            circuit.call(lambda: "success")
```

## Error Response Standards

### 1. API Error Response Format
**Standard:** Consistent error response structure across all APIs.

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": {
      "field": "email",
      "reason": "Invalid email format"
    },
    "timestamp": "2024-01-15T10:30:00Z",
    "request_id": "req_123456789"
  }
}
```

### 2. HTTP Status Code Guidelines
**Standards:** Use appropriate HTTP status codes for different error types.

```python
ERROR_STATUS_CODES = {
    ValidationError: 400,           # Bad Request
    AuthenticationError: 401,       # Unauthorized
    PermissionError: 403,          # Forbidden
    NotFoundError: 404,            # Not Found
    BusinessLogicError: 422,       # Unprocessable Entity
    RateLimitError: 429,           # Too Many Requests
    ExternalServiceError: 502,     # Bad Gateway
    ServiceUnavailableError: 503,  # Service Unavailable
    TimeoutError: 504,             # Gateway Timeout
    Exception: 500                 # Internal Server Error
}

def handle_api_error(error):
    """Convert error to appropriate HTTP response."""
    status_code = ERROR_STATUS_CODES.get(type(error), 500)
    
    response = {
        "error": {
            "code": type(error).__name__.upper(),
            "message": str(error),
            "timestamp": datetime.utcnow().isoformat()
        }
    }
    
    return response, status_code
```

## AI Assistant Error Handling Guidelines

### 1. When Implementing Error Handling
Always include:
- [ ] Input validation with clear error messages
- [ ] Appropriate exception types for different failure modes
- [ ] Logging with sufficient context for debugging
- [ ] User-friendly error responses
- [ ] Graceful degradation where possible

### 2. When Testing Error Scenarios
Ensure coverage of:
- [ ] Invalid input handling
- [ ] Network failure scenarios
- [ ] Database connectivity issues
- [ ] External service failures
- [ ] Rate limiting and timeout handling

### 3. When Designing APIs
Consider:
- [ ] Consistent error response format
- [ ] Appropriate HTTP status codes
- [ ] Rate limiting and quota management
- [ ] Circuit breaker patterns for external dependencies
- [ ] Comprehensive error documentation

## Quick Reference

**Validate Early:** Check inputs immediately and fail fast with clear messages
**Categorize Errors:** Use specific exception types for different failure modes
**Retry Smart:** Implement exponential backoff for transient failures
**Degrade Gracefully:** Provide reduced functionality rather than complete failure
**Monitor Everything:** Track error rates and patterns for proactive response
**Test Failures:** Systematically test error scenarios and edge cases

For implementation examples, see [`QUICK_REFERENCE_CARDS.md`](QUICK_REFERENCE_CARDS.md). 