# Monitoring and Observability Guide

This document describes monitoring and observability for SkillsJars Spring AI Example.

## Overview

This is a **CLI application** that runs locally. Monitoring differs from traditional web services:

| Aspect | Traditional Service | This CLI Application |
|--------|---------------------|---------------------|
| Availability | Uptime monitoring | N/A (user-initiated) |
| Performance | Response time metrics | Console output timing |
| Errors | Error tracking service | Console error messages |
| Usage | Analytics/telemetry | User self-reporting |

## Built-in Observability

### MyLoggingAdvisor

The application includes `MyLoggingAdvisor` which provides visibility into AI interactions:

**What it logs:**
- System prompts (truncated to 6000 chars)
- Available tools
- User input (last message)
- Tool calls with arguments
- Tool responses (truncated to 3000 chars)
- Assistant text responses (truncated to 2000 chars)

**Example output:**
```
USER: 
 - SYSTEM: When calling Skills, the toolname is "Skill"
 - TOOLS: ["pdf_skill", "shell_tool", "fs_tool"]
 - TEXT: create a pdf of directory listing

ASSISTANT: 
 - TOOL-CALL: shell_tool ({"command": "ls -la"})
 - TEXT: I'll create a PDF with the directory listing...
```

### Security Redaction

Sensitive data is automatically redacted from logs:

```
Before: api_key="sk-abc123def456..."
After:  api_key=[REDACTED]
```

## Local Debugging

### Enable Verbose Logging

The application logs by default. To customize:

```java
MyLoggingAdvisor.builder()
    .showSystemMessage(true)      // Show system prompts
    .showAvailableTools(true)     // Show tool list
    .showUserText(true)           // Show user input
    .showAssistantText(true)      // Show AI responses
    .build()
```

### Analyze Logs

1. **Redirect output to file**
   ```bash
   ./mvnw spring-boot:run > output.log 2>&1
   ```

2. **Search for specific patterns**
   ```bash
   grep "TOOL-CALL" output.log
   grep "ERROR" output.log
   ```

3. **Filter sensitive data**
   ```bash
   # Verify redaction is working
   grep -E "(api_key|token|secret)" output.log
   # Should only show [REDACTED]
   ```

## Error Handling

### Application Errors

The application handles these error scenarios:

| Error Type | User Message | Logged |
|------------|--------------|--------|
| Missing API key | Error message with setup instructions | Yes |
| Invalid API key | OpenAI API error (401) | Yes |
| Network error | Connection error message | Yes |
| Tool execution failure | Tool-specific error | Yes |
| Invalid user input | AI-generated helpful response | Yes |

### Common Error Patterns

**Missing Environment Variables:**
```
Error: OPENAI_API_KEY not set
Solution: Set environment variables before running
```

**Rate Limiting:**
```
Error: 429 Too Many Requests
Solution: Implement retry logic or reduce request frequency
```

**Tool Execution Failure:**
```
Error: Shell command failed with exit code 1
Solution: Check command syntax and permissions
```

## Health Checks

### Manual Verification

Since this is a CLI app, users perform manual health checks:

1. **Startup check**
   ```bash
   ./mvnw spring-boot:run
   # Should display: "I am your assistant."
   ```

2. **Basic interaction**
   ```
   > USER: hello
   # Should receive AI response
   ```

3. **Tool execution**
   ```
   > USER: list files in current directory
   # Should execute and show results
   ```

## CI/CD Monitoring

### GitHub Actions

Monitor CI pipeline health:

1. **Workflow runs**: https://github.com/zhijunio/skillsjars-example-spring-ai/actions
2. **Build status badge**: ![CI](https://github.com/zhijunio/skillsjars-example-spring-ai/actions/workflows/ci.yml/badge.svg)

### Pipeline Checks

| Check | Frequency | Alert Threshold |
|-------|-----------|-----------------|
| Build passes | Every PR/push | Any failure |
| Tests pass | Every PR/push | Any failure |
| Security scan | Every PR/push | Critical/High vulnerabilities |
| Dependency check | Weekly | Critical vulnerabilities |

## Production Deployment Monitoring

For a CLI application, "production" is user environments. Monitor indirectly:

### GitHub Issues

- Track bug reports
- Monitor feature requests
- Identify common setup problems

### Release Metrics

| Metric | Source | Frequency |
|--------|--------|-----------|
| Release downloads | GitHub Releases | Per release |
| Maven downloads | Maven Central dashboard | Weekly |
| Star count trend | GitHub | Monthly |

### User Feedback

- GitHub Issues for bug reports
- Email: zhijun.lab@gmail.com
- PR contributions (indicator of adoption)

## Observability Checklist

Before release, verify:

- [ ] MyLoggingAdvisor outputs expected information
- [ ] Sensitive data is redacted from logs
- [ ] Error messages are actionable
- [ ] Startup succeeds with valid configuration
- [ ] CI pipeline provides visibility into build health
- [ ] GitHub Issues enabled for user feedback

## Future Enhancements

Consider adding:

1. **Structured logging** (JSON format for log aggregation)
2. **Telemetry opt-in** (anonymous usage statistics)
3. **Health check command** (`./mvnw spring-boot:run --health`)
4. **Verbose mode** (`--verbose` flag for debug logging)
5. **Log file output** (optional file logging for debugging sessions)

---

For more information on logging configuration, see `README.md#configuration`.
