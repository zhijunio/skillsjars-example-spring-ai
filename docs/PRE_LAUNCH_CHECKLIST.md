# Pre-Launch Checklist

Use this checklist before deploying to production.

## Code Quality

- [ ] All tests pass (unit, integration, e2e)
- [ ] Build succeeds with no warnings
- [ ] Lint and type checking pass
- [ ] Code reviewed and approved
- [ ] No TODO comments that should be resolved before launch
- [ ] No debugging statements in production code
- [ ] Error handling covers expected failure modes

## Security

- [x] No secrets in code or version control
- [x] `mvnw dependency-check:check` shows no critical vulnerabilities
- [ ] Input validation on all user-facing endpoints
- [x] Authentication and authorization checks in place (API key required)
- [ ] Rate limiting configured
- [x] Environment variables for sensitive configuration (not hardcoded)

## Performance

- [ ] No N+1 queries in critical paths
- [ ] Database queries have appropriate indexes
- [ ] Connection pooling configured

## Infrastructure

- [x] Environment variables documented (see README.md)
- [ ] Production environment variables set
- [ ] Database migrations applied (if applicable)
- [ ] DNS and SSL configured (if applicable)
- [ ] Health check endpoint exists
- [x] Logging configured (MyLoggingAdvisor)

## Documentation

- [x] README updated with setup requirements
- [x] API documentation current
- [x] CHANGELOG.md updated
- [x] CONTRIBUTING.md available
- [x] SECURITY.md available
- [x] CICD.md documents deployment process

## Monitoring

- [ ] Error reporting configured
- [ ] Metrics dashboard set up
- [ ] Alert thresholds defined
- [ ] Log aggregation configured

## Rollback Plan

- [ ] Previous version available for quick rollback
- [ ] Database rollback procedure documented (if applicable)
- [ ] Feature flag strategy (if applicable)
- [ ] Team notified of deployment schedule

## Notes for This Project

This is a **CLI demonstration application** with the following characteristics:

| Aspect | Status | Notes |
|--------|--------|-------|
| Type | CLI Application | No web server, runs locally |
| Deployment | Local execution | Users run via `./mvnw spring-boot:run` |
| Database | None | No persistence layer |
| Authentication | API Key (OpenAI) | Via environment variables |
| Monitoring | Console logging | MyLoggingAdvisor provides visibility |
| Feature Flags | Not applicable | Single-purpose demo |

## Launch Readiness Status

| Component | Status | Notes |
|-----------|--------|-------|
| Code Quality | ✅ Ready | Build passes, code reviewed |
| Security | ✅ Ready | Secrets externalized, OWASP check in CI |
| Documentation | ✅ Ready | Comprehensive docs in place |
| CI/CD | ✅ Ready | Full pipeline configured |
| Monitoring | ⚠️ Basic | Console logging only (appropriate for CLI) |
| Rollback | ✅ Ready | Git-based versioning |

## Environment Variables Required

```bash
# Required for application to function
export OPENAI_API_BASE_URL=https://api.openai.com/v1
export OPENAI_API_KEY=your-api-key-here
export OPENAI_API_MODEL=gpt-4o
```

## Deployment Steps

1. **Clone repository**
   ```bash
   git clone https://github.com/zhijunio/skillsjars-example-spring-ai.git
   cd skillsjars-example-spring-ai
   ```

2. **Set environment variables**
   ```bash
   export OPENAI_API_BASE_URL=...
   export OPENAI_API_KEY=...
   export OPENAI_API_MODEL=...
   ```

3. **Run application**
   ```bash
   ./mvnw spring-boot:run
   ```

## Post-Launch Verification

After deployment (users running locally):

- [ ] Application starts without errors
- [ ] Help message displays correctly
- [ ] Sample commands execute successfully
- [ ] Error messages are helpful and actionable
- [ ] No sensitive data logged (verify MyLoggingAdvisor redaction)

---

**Launch Approval**

| Role | Name | Date | Status |
|------|------|------|--------|
| Engineering | | | [ ] Approved |
| Security | | | [ ] Approved |
| Documentation | | | [ ] Approved |
