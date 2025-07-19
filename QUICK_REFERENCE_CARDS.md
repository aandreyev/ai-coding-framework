# Quick Reference Cards

These cards provide essential information from our strategy documents at a glance. Use them for quick reference during development, and dive into the full documents when you need detailed guidance.

---

## 🛡️ Security Strategy Quick Card

**Key Principles:**
- Validate all inputs at system boundaries
- Use parameterized queries for database access
- Implement proper authentication and authorization
- Log security events for monitoring

**Essential Checklist:**
- [ ] Input validation and sanitization
- [ ] SQL injection prevention
- [ ] XSS protection in web apps
- [ ] Secure credential storage
- [ ] HTTPS for data transmission
- [ ] Rate limiting for APIs

**When to Reference Full Document:**
- Building authentication systems
- Handling sensitive data
- Integrating external APIs
- Processing user uploads

**Full Document:** [`SECURITY_STRATEGY.md`](SECURITY_STRATEGY.md)

---

## ⚡ Error Resilience Quick Card

**Key Principles:**
- Fail fast with clear error messages
- Implement graceful degradation
- Use circuit breakers for external services
- Log errors with context for debugging

**Essential Patterns:**
- **Try-Catch:** Handle expected errors gracefully
- **Validation:** Check inputs before processing
- **Fallbacks:** Provide alternative behavior when services fail
- **Timeouts:** Don't wait indefinitely for responses

**Error Response Structure:**
```json
{
  "error": "user_friendly_message",
  "code": "ERROR_CODE",
  "details": "technical_details",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

**Full Document:** [`ERROR_RESILIENCE_STRATEGY.md`](ERROR_RESILIENCE_STRATEGY.md)

---

## 📊 Code Quality Quick Card

**Key Standards:**
- Write self-documenting code with clear names
- Keep functions small and focused (< 50 lines)
- Use consistent formatting and style
- Include docstrings for public functions

**Code Review Checklist:**
- [ ] Functions have single responsibility
- [ ] Variable names are descriptive
- [ ] No magic numbers or strings
- [ ] Proper error handling
- [ ] Unit tests for new logic

**Documentation Requirements:**
- README with setup instructions
- API documentation for public interfaces
- Inline comments for complex logic
- Architecture decision records for major choices

**Full Document:** [`CODE_QUALITY_STRATEGY.md`](CODE_QUALITY_STRATEGY.md)

---

## 🧪 Testing Strategy Quick Card

**Testing Pyramid:**
- **Unit Tests (70%):** Test individual functions/methods
- **Integration Tests (20%):** Test component interactions
- **E2E Tests (10%):** Test complete user workflows

**Essential Test Types:**
- **Happy Path:** Normal operation with valid inputs
- **Edge Cases:** Boundary conditions and limits
- **Error Cases:** Invalid inputs and failure scenarios
- **Performance:** Response times and resource usage

**Test Naming Convention:**
```
test_[function_name]_[scenario]_[expected_result]
test_calculate_discount_with_valid_coupon_returns_discounted_price
```

**Full Document:** [`TESTING_STRATEGY.md`](TESTING_STRATEGY.md)

---

## 🚀 Deployment Strategy Quick Card

**Key Principles:**
- Automate everything (build, test, deploy)
- Use blue-green deployments for zero downtime
- Implement health checks and monitoring
- Have rollback procedures ready

**Deployment Checklist:**
- [ ] All tests pass in CI/CD pipeline
- [ ] Database migrations tested
- [ ] Environment variables configured
- [ ] Health checks respond correctly
- [ ] Monitoring and alerts configured
- [ ] Rollback plan documented

**Environment Progression:**
Development → Staging → Production

Each environment should be as similar as possible.

**Full Document:** [`DEPLOYMENT_STRATEGY.md`](DEPLOYMENT_STRATEGY.md)

---

## 📈 Monitoring Strategy Quick Card

**Essential Metrics:**
- **Performance:** Response times, throughput
- **Errors:** Error rates, failure patterns
- **Resources:** CPU, memory, disk usage
- **Business:** User actions, conversions

**Alerting Priorities:**
- **Critical:** Service down, data loss
- **Warning:** High error rates, slow responses
- **Info:** Deployment events, scaling actions

**Log Levels:**
- **ERROR:** System failures, exceptions
- **WARN:** Recoverable problems
- **INFO:** Important events, user actions
- **DEBUG:** Detailed troubleshooting info

**Full Document:** [`MONITORING_STRATEGY.md`](MONITORING_STRATEGY.md)

---

## 🗃️ Data Migration Quick Card

**Key Principles:**
- Always backup before migration
- Test migrations on copy of production data
- Make migrations reversible when possible
- Run migrations during low-traffic periods

**Migration Checklist:**
- [ ] Backup current data
- [ ] Test migration on staging
- [ ] Prepare rollback script
- [ ] Schedule maintenance window
- [ ] Monitor during migration
- [ ] Validate data integrity after

**Best Practices:**
- Use database transactions for atomicity
- Implement data validation checks
- Log all migration activities
- Have customer communication plan

**Full Document:** [`DATA_MIGRATION_STRATEGY.md`](DATA_MIGRATION_STRATEGY.md)

---

## 📝 Logging Strategy Quick Card

**Log Structure:**
```json
{
  "timestamp": "2024-01-01T00:00:00Z",
  "level": "INFO",
  "message": "User login successful",
  "user_id": "123",
  "correlation_id": "abc-def-ghi"
}
```

**What to Log:**
- **Authentication events:** Login/logout attempts
- **Business events:** Orders, payments, user actions  
- **System events:** Startup, shutdown, configuration changes
- **Errors:** Exceptions with full stack traces

**What NOT to Log:**
- Passwords or sensitive credentials
- Personal data (unless required for debugging)
- High-frequency debug info in production

**Full Document:** [`LOGGING_STRATEGY.md`](LOGGING_STRATEGY.md)

---

## Navigation

**Need Full Guidance?** See [`FRAMEWORK_INDEX.md`](FRAMEWORK_INDEX.md)

**Start a Task?** Use [`AI_ORCHESTRATION_PROMPT.md`](AI_ORCHESTRATION_PROMPT.md) or [`FRAMEWORK_LITE.md`](FRAMEWORK_LITE.md) 