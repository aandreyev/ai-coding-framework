# Testing Strategy

This document defines comprehensive testing practices for AI assistants to ensure code quality, reliability, and maintainability. Follow these patterns to build robust test suites that catch issues early and support confident refactoring.

## Core Testing Principles

### 1. Test Pyramid Structure
**Rule:** Follow the test pyramid to balance speed, reliability, and coverage.

```
    /\     E2E Tests (10%)
   /  \    Slow, expensive, realistic
  /____\   
 /      \  Integration Tests (20%)
/________\  Medium speed, test interactions
\        /
 \______/   Unit Tests (70%)
  \____/    Fast, isolated, focused
```

**Implementation Guidelines:**
- **Unit Tests (70%):** Test individual functions and methods in isolation
- **Integration Tests (20%):** Test component interactions and data flow
- **End-to-End Tests (10%):** Test complete user workflows and system behavior

### 2. Test-Driven Development (TDD)
**Rule:** Write tests before implementation to drive design and ensure coverage.

```python
# TDD Cycle: Red -> Green -> Refactor

# 1. RED: Write failing test first
def test_calculate_discount_with_valid_coupon():
    """Test discount calculation with valid coupon code."""
    result = calculate_discount(100.0, "SAVE10")
    assert result == 90.0

# 2. GREEN: Write minimal code to make test pass
def calculate_discount(amount, coupon_code):
    if coupon_code == "SAVE10":
        return amount * 0.9
    return amount

# 3. REFACTOR: Improve code while keeping tests green
def calculate_discount(amount, coupon_code):
    """Calculate discounted amount based on coupon code."""
    discounts = {"SAVE10": 0.1, "SAVE20": 0.2}
    discount_rate = discounts.get(coupon_code, 0)
    return amount * (1 - discount_rate)
```

### 3. Test Categories and Coverage
**Rule:** Test all paths including happy path, edge cases, and error conditions.

```python
class TestUserRegistration:
    """Comprehensive test suite for user registration."""
    
    # Happy path tests
    def test_register_user_with_valid_data_succeeds(self):
        user = register_user("john@example.com", "SecurePass123!")
        assert user.email == "john@example.com"
        assert user.is_active is True
    
    # Edge case tests
    def test_register_user_with_minimum_valid_password(self):
        user = register_user("jane@example.com", "Pass123!")
        assert user is not None
    
    def test_register_user_with_maximum_email_length(self):
        long_email = "a" * 240 + "@example.com"  # 254 chars total
        user = register_user(long_email, "SecurePass123!")
        assert user.email == long_email
    
    # Error condition tests
    def test_register_user_with_invalid_email_raises_error(self):
        with pytest.raises(ValidationError, match="Invalid email format"):
            register_user("invalid-email", "SecurePass123!")
    
    def test_register_user_with_existing_email_raises_error(self):
        register_user("existing@example.com", "Pass123!")
        with pytest.raises(BusinessLogicError, match="Email already registered"):
            register_user("existing@example.com", "Pass456!")
    
    def test_register_user_with_weak_password_raises_error(self):
        with pytest.raises(ValidationError, match="Password too weak"):
            register_user("user@example.com", "123")
```

## Unit Testing Patterns

### 1. Isolated Unit Tests
**Pattern:** Test units in isolation using mocks for dependencies.

```python
import pytest
from unittest.mock import Mock, patch

class TestEmailService:
    def test_send_welcome_email_calls_smtp_correctly(self):
        """Test email service uses SMTP client correctly."""
        # Arrange
        mock_smtp = Mock()
        email_service = EmailService(smtp_client=mock_smtp)
        user = User(email="test@example.com", name="Test User")
        
        # Act
        result = email_service.send_welcome_email(user)
        
        # Assert
        mock_smtp.send.assert_called_once_with(
            to="test@example.com",
            subject="Welcome to Our Service",
            body="Hello Test User, welcome to our service!"
        )
        assert result is True
    
    @patch('email_service.smtp_client')
    def test_send_email_handles_smtp_failure(self, mock_smtp):
        """Test email service handles SMTP failures gracefully."""
        # Arrange
        mock_smtp.send.side_effect = SMTPException("Server unavailable")
        email_service = EmailService()
        
        # Act & Assert
        with pytest.raises(EmailDeliveryError):
            email_service.send_welcome_email(User(email="test@example.com"))
```

### 2. Parameterized Tests
**Pattern:** Test multiple scenarios efficiently with parameterized tests.

```python
import pytest

class TestPasswordValidator:
    @pytest.mark.parametrize("password,expected", [
        ("SecurePass123!", True),      # Valid password
        ("AnotherGood1@", True),       # Valid password
        ("short", False),              # Too short
        ("nouppercase123!", False),    # No uppercase
        ("NOLOWERCASE123!", False),    # No lowercase
        ("NoNumbers!", False),         # No numbers
        ("NoSpecialChars123", False),  # No special characters
    ])
    def test_password_validation(self, password, expected):
        """Test password validation with various inputs."""
        result = validate_password(password)
        assert result == expected
    
    @pytest.mark.parametrize("email,expected", [
        ("user@example.com", True),
        ("test.email+tag@domain.co.uk", True),
        ("invalid.email", False),
        ("@missingusername.com", False),
        ("username@.com", False),
        ("", False),
        (None, False),
    ])
    def test_email_validation(self, email, expected):
        """Test email validation with various formats."""
        result = validate_email(email)
        assert result == expected
```

## Integration Testing Patterns

### 1. Database Integration Tests
**Pattern:** Test database operations with real database connections.

```python
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

@pytest.fixture(scope="function")
def db_session():
    """Create test database session."""
    engine = create_engine("sqlite:///:memory:")  # In-memory test database
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)
    session = Session()
    
    yield session
    
    session.close()

class TestUserRepository:
    def test_create_user_saves_to_database(self, db_session):
        """Test user creation persists to database."""
        # Arrange
        repo = UserRepository(db_session)
        user_data = {"email": "test@example.com", "name": "Test User"}
        
        # Act
        user = repo.create_user(user_data)
        
        # Assert
        saved_user = db_session.query(User).filter_by(email="test@example.com").first()
        assert saved_user is not None
        assert saved_user.email == "test@example.com"
        assert saved_user.name == "Test User"
    
    def test_find_user_by_email_returns_correct_user(self, db_session):
        """Test user lookup by email."""
        # Arrange
        repo = UserRepository(db_session)
        user1 = User(email="user1@example.com", name="User One")
        user2 = User(email="user2@example.com", name="User Two")
        db_session.add_all([user1, user2])
        db_session.commit()
        
        # Act
        found_user = repo.find_by_email("user1@example.com")
        
        # Assert
        assert found_user.email == "user1@example.com"
        assert found_user.name == "User One"
```

### 2. API Integration Tests
**Pattern:** Test API endpoints with real HTTP requests.

```python
import pytest
import requests
from fastapi.testclient import TestClient

@pytest.fixture
def api_client():
    """Create test API client."""
    return TestClient(app)

class TestUserAPI:
    def test_create_user_endpoint_returns_201(self, api_client):
        """Test user creation endpoint."""
        # Arrange
        user_data = {
            "email": "newuser@example.com",
            "name": "New User",
            "password": "SecurePass123!"
        }
        
        # Act
        response = api_client.post("/api/users", json=user_data)
        
        # Assert
        assert response.status_code == 201
        response_data = response.json()
        assert response_data["email"] == "newuser@example.com"
        assert "password" not in response_data  # Ensure password not returned
    
    def test_create_user_with_invalid_data_returns_400(self, api_client):
        """Test user creation with invalid data."""
        # Arrange
        invalid_data = {"email": "invalid-email", "name": ""}
        
        # Act
        response = api_client.post("/api/users", json=invalid_data)
        
        # Assert
        assert response.status_code == 400
        assert "error" in response.json()
```

## End-to-End Testing Patterns

### 1. User Workflow Tests
**Pattern:** Test complete user journeys from start to finish.

```python
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

@pytest.fixture
def browser():
    """Set up browser for E2E tests."""
    driver = webdriver.Chrome()
    driver.implicitly_wait(10)
    yield driver
    driver.quit()

class TestUserRegistrationFlow:
    def test_complete_user_registration_workflow(self, browser):
        """Test complete user registration from signup to welcome."""
        # Navigate to registration page
        browser.get("http://localhost:3000/register")
        
        # Fill registration form
        email_input = browser.find_element(By.ID, "email")
        email_input.send_keys("testuser@example.com")
        
        password_input = browser.find_element(By.ID, "password")
        password_input.send_keys("SecurePass123!")
        
        submit_button = browser.find_element(By.ID, "submit")
        submit_button.click()
        
        # Wait for redirect to welcome page
        WebDriverWait(browser, 10).until(
            EC.presence_of_element_located((By.ID, "welcome-message"))
        )
        
        # Verify welcome message
        welcome_text = browser.find_element(By.ID, "welcome-message").text
        assert "Welcome, testuser@example.com" in welcome_text
        
        # Verify user can access dashboard
        dashboard_link = browser.find_element(By.ID, "dashboard-link")
        dashboard_link.click()
        
        WebDriverWait(browser, 10).until(
            EC.presence_of_element_located((By.ID, "dashboard"))
        )
        
        assert "dashboard" in browser.current_url
```

## Test Configuration and Setup

### 1. Test Environment Configuration
**Setup:** Configure isolated test environments.

```python
# conftest.py - pytest configuration
import pytest
import os
from unittest.mock import patch

@pytest.fixture(scope="session")
def test_config():
    """Test-specific configuration."""
    return {
        "DATABASE_URL": "sqlite:///:memory:",
        "REDIS_URL": "redis://localhost:6379/1",  # Test database
        "EMAIL_BACKEND": "test",
        "DEBUG": True,
        "TESTING": True
    }

@pytest.fixture(autouse=True)
def setup_test_environment(test_config):
    """Automatically set up test environment for all tests."""
    with patch.dict(os.environ, test_config):
        yield

@pytest.fixture(scope="function")
def clean_database(db_session):
    """Clean database before each test."""
    # Clear all tables
    for table in reversed(Base.metadata.sorted_tables):
        db_session.execute(table.delete())
    db_session.commit()
    yield
    # Cleanup after test
    db_session.rollback()
```

### 2. Test Data Management
**Pattern:** Use factories and fixtures for consistent test data.

```python
import factory
from factory.alchemy import SQLAlchemyModelFactory

class UserFactory(SQLAlchemyModelFactory):
    """Factory for creating test users."""
    class Meta:
        model = User
        sqlalchemy_session_persistence = "commit"
    
    email = factory.Sequence(lambda n: f"user{n}@example.com")
    name = factory.Faker("name")
    is_active = True
    created_at = factory.Faker("date_time")

class ProductFactory(SQLAlchemyModelFactory):
    """Factory for creating test products."""
    class Meta:
        model = Product
        sqlalchemy_session_persistence = "commit"
    
    name = factory.Faker("word")
    price = factory.Faker("pydecimal", left_digits=3, right_digits=2, positive=True)
    category = factory.Faker("word")

# Usage in tests
class TestOrderService:
    def test_create_order_with_valid_data(self, db_session):
        # Create test data using factories
        user = UserFactory()
        product1 = ProductFactory(price=10.00)
        product2 = ProductFactory(price=20.00)
        
        # Test order creation
        order = create_order(user.id, [product1.id, product2.id])
        
        assert order.total == 30.00
        assert len(order.items) == 2
```

## Test Quality and Maintenance

### 1. Test Coverage Requirements
**Standard:** Maintain comprehensive test coverage across all code paths.

```yaml
# pytest.ini - Coverage configuration
[tool:pytest]
addopts = 
    --cov=src
    --cov-report=html
    --cov-report=term-missing
    --cov-fail-under=80
    --strict-markers

markers =
    unit: Unit tests
    integration: Integration tests
    e2e: End-to-end tests
    slow: Slow running tests
```

### 2. Performance Testing
**Pattern:** Include performance tests for critical paths.

```python
import time
import pytest

class TestPerformance:
    def test_user_lookup_performance(self, db_session):
        """Test user lookup completes within acceptable time."""
        # Setup: Create many users
        users = UserFactory.create_batch(1000)
        db_session.commit()
        
        # Test: Measure lookup time
        start_time = time.time()
        result = find_user_by_email("user500@example.com")
        end_time = time.time()
        
        # Assert: Lookup should be fast
        assert result is not None
        assert (end_time - start_time) < 0.1  # Under 100ms
    
    @pytest.mark.slow
    def test_bulk_import_performance(self):
        """Test bulk operations perform within limits."""
        data = [{"name": f"Product {i}", "price": i} for i in range(10000)]
        
        start_time = time.time()
        bulk_import_products(data)
        end_time = time.time()
        
        # Should process 10k items in under 5 seconds
        assert (end_time - start_time) < 5.0
```

## AI Assistant Testing Guidelines

### 1. When Writing Tests
Always include:
- [ ] Test for happy path with valid inputs
- [ ] Test edge cases and boundary conditions
- [ ] Test error conditions and exception handling
- [ ] Use descriptive test names that explain the scenario
- [ ] Include setup and teardown for clean test isolation

### 2. Test Structure Standards
Follow the Arrange-Act-Assert pattern:
- [ ] **Arrange:** Set up test data and conditions
- [ ] **Act:** Execute the function or method being tested
- [ ] **Assert:** Verify the expected outcomes

### 3. Testing Checklist
Before considering code complete:
- [ ] Unit tests cover all public methods
- [ ] Integration tests verify component interactions
- [ ] Error handling is tested with appropriate exceptions
- [ ] Edge cases and boundary conditions are covered
- [ ] Test coverage meets minimum requirements (80%)

## Quick Reference

**Test Pyramid:** 70% unit, 20% integration, 10% E2E tests
**TDD Cycle:** Red (write failing test) → Green (make it pass) → Refactor
**Test Categories:** Happy path, edge cases, error conditions
**Naming:** `test_function_scenario_expected_result`
**Structure:** Arrange → Act → Assert
**Coverage:** Maintain >80% code coverage with meaningful tests

For implementation examples, see [`QUICK_REFERENCE_CARDS.md`](QUICK_REFERENCE_CARDS.md). 