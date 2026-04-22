# SkillsJars Spring AI Example

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://adoptium.net/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.0.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring AI](https://img.shields.io/badge/Spring%20AI-2.0.0--M4-blue.svg)](https://spring.io/projects/spring-ai)
[![CI](https://github.com/zhijunio/skillsjars-example-spring-ai/actions/workflows/ci.yml/badge.svg)](https://github.com/zhijunio/skillsjars-example-spring-ai/actions/workflows/ci.yml)

This example demonstrates how to integrate [SkillsJars](https://www.skillsjars.com) with [Spring AI Agent Utils](https://github.com/spring-ai-community/spring-ai-agent-utils) to enable AI agents to use various Agent Skills through dependency-defined configuration.

## Features

- **SkillsJars Integration** - Quickly integrate pre-built AI skills via Maven dependencies
- **Spring AI Support** - Built on Spring AI 2.0, supports OpenAI-compatible APIs
- **Tool Calling** - Supports Shell commands, file system operations, PDF generation, and more
- **Interactive CLI** - Command-line interface with multi-turn conversation support
- **Logging & Debugging** - Custom Advisor displays tool calls and responses with sensitive data redaction

## Tech Stack

| Component | Version |
|-----------|---------|
| Java | 21 |
| Spring Boot | 4.0.5 |
| Spring AI | 2.0.0-M4 |
| spring-ai-agent-utils | 0.7.0 |
| SkillsJars PDF | 2026_02_25-3d59511 |

## Quick Start

### Prerequisites

- JDK 21+
- Maven 3.9+
- OpenAI API Key or compatible LLM API

### Configure Environment Variables

**Option 1: Using .env file (Recommended)**

1. Copy `.env.example` to `.env`:
   ```bash
   cp .env.example .env
   ```

2. Edit `.env` and fill in your values:
   ```
   OPENAI_API_BASE_URL=https://api.openai.com/v1
   OPENAI_API_KEY=sk-your-api-key-here
   OPENAI_API_MODEL=gpt-4o
   ```

3. Load environment variables:
   ```bash
   # Using dotenv-cli
   brew install dotenv-cli                                                                                                                                                
   dotenv -- ./mvnw spring-boot:run

   # Or using shell
   set -a && source .env && set +a && ./mvnw spring-boot:run
   ```

**Option 2: Export variables manually**

```bash
# macOS / Linux
export OPENAI_API_BASE_URL=https://api.openai.com/v1
export OPENAI_API_KEY=your-api-key-here
export OPENAI_API_MODEL=gpt-4o

# Windows (PowerShell)
$env:OPENAI_API_BASE_URL="https://api.openai.com/v1"
$env:OPENAI_API_KEY="your-api-key-here"
$env:OPENAI_API_MODEL="gpt-4o"
```

> **Note:** `.env` files are gitignored. Never commit `.env` to version control.

### Run the Application

```bash
./mvnw spring-boot:run
```

After startup, you will see an interactive prompt:

```
I am your assistant.

> USER:
```

### Usage Examples

Interact with the AI using natural language commands:

```
> USER: use the pdf skill to create a pdf containing the current directory listing

> USER: list all Java files in the current directory

> USER: read the contents of pom.xml
```

## Project Structure

```
skillsjars-example-spring-ai/
├── src/main/java/org/springaicommunity/agent/
│   ├── Application.java              # Main entry point and CLI runner
│   └── MyLoggingAdvisor.java         # Custom logging Advisor
├── src/main/resources/
│   └── application.properties        # Application configuration
├── .github/
│   ├── ISSUE_TEMPLATE/               # Issue templates (bug, feature)
│   ├── PULL_REQUEST_TEMPLATE.md      # PR submission template
│   ├── workflows/
│   │   ├── ci.yml                    # Main CI build pipeline
│   │   ├── pr-validation.yml         # PR validation checks
│   │   ├── publish-snapshot.yml      # Auto-publish snapshots
│   │   └── release.yml               # Manual release workflow
│   └── CODEOWNERS                    # Code review responsibilities
├── docs/
│   ├── CICD.md                       # CI/CD guide and documentation
│   ├── MONITORING.md                 # Observability and monitoring guide
│   ├── PRE_LAUNCH_CHECKLIST.md       # Pre-launch verification checklist
│   └── RELEASE_RUNBOOK.md            # Step-by-step release instructions
├── .env.example                      # Environment variable template
├── LICENSE                           # Apache 2.0 License
├── pom.xml                           # Maven build configuration
├── README.md                         # Project documentation
├── CHANGELOG.md                      # Version history
├── CONTRIBUTING.md                   # Contribution guidelines
├── SECURITY.md                       # Security policy
└── .editorconfig                     # Editor configuration
```

## Core Components

### Application.java

Main application class responsible for:

- Building `ChatClient` with default tools configuration (`buildChatClient()`)
- Registering `SkillsTool`, `ShellTools`, `FileSystemTools`
- Running the interactive CLI loop (`runInteractiveLoop()`)

### MyLoggingAdvisor.java

Custom Spring AI Advisor for:

- Logging user inputs and assistant responses
- Displaying available tools
- Showing tool calls and responses (with automatic sensitive data redaction)
- Key methods:
  - `before()` - Log request before sending to AI
  - `after()` - Log response after receiving from AI
  - `redactSensitiveData()` - Filter API keys and tokens from logs

### Configuration

`application.properties` configuration items:

```properties
# Disable web mode, use CLI
spring.main.web-application-type=none

# OpenAI configuration (via environment variables)
spring.ai.openai-sdk.base-url=${OPENAI_API_BASE_URL}
spring.ai.openai-sdk.api-key=${OPENAI_API_KEY}
spring.ai.openai-sdk.chat.options.model=${OPENAI_API_MODEL}

# SkillsJars skills path
agent.skills.paths=classpath:/META-INF/skills
```

## Adding New Skills

1. Add SkillsJars dependency in `pom.xml`:

```xml
<dependency>
    <groupId>com.skillsjars</groupId>
    <artifactId>anthropics__skills__{skill-name}</artifactId>
    <version>{version}</version>
</dependency>
```

2. Skills will be automatically discovered and registered by `SkillsTool`

## Release to Maven Central

The project is configured with automated release workflow to Maven Central.

### Manual Release

```bash
./mvnw clean deploy -P release
```

### Using GitHub Actions

1. Go to Actions → Release
2. Enter version number (e.g., `0.1.0`)
3. Run the workflow

### Required Secrets

Configure these in GitHub Repository Settings → Settings → Secrets and variables → Actions:

| Secret | Description |
|--------|-------------|
| `MAVEN_USERNAME` | Sonatype Central username |
| `MAVEN_PASSWORD` | Sonatype Central password |
| `GPG_SECRET_KEY` | GPG private key (armored) |
| `GPG_PASSPHRASE` | GPG private key passphrase |

> See [docs/SECREETS.md](docs/SECRETS.md) for detailed setup instructions.

## Security Considerations

> **Important**: This project includes `ShellTools` for demonstration purposes, which allows arbitrary shell command execution.

- **Production Use**: Do not use `ShellTools` directly in production, or implement command whitelisting
- **API Keys**: Always use environment variables, never commit to version control
- **Log Redaction**: `MyLoggingAdvisor` automatically filters sensitive information from logs

## FAQ

### Q: Which LLM providers are supported?

A: Any OpenAI-compatible API is supported, including:
- OpenAI
- Azure OpenAI
- Local models (Ollama, vLLM, etc.)

### Q: How to customize logging level?

A: Modify the `MyLoggingAdvisor` builder configuration:

```java
MyLoggingAdvisor.builder()
    .showSystemMessage(true)
    .showAvailableTools(true)
    .showUserText(true)
    .showAssistantText(true)
    .build()
```

### Q: How do I configure environment variables with .env?

A: The project includes a `.env.example` template file:

1. **Copy the template:**
   ```bash
   cp .env.example .env
   ```

2. **Fill in your values:**
   ```bash
   OPENAI_API_BASE_URL=https://api.openai.com/v1
   OPENAI_API_KEY=sk-your-actual-api-key
   OPENAI_API_MODEL=gpt-4o
   ```

3. **Load and run:**
   ```bash
   # Option A: Using dotenv-cli (install: brew install dotenv-cli )
   dotenv -- ./mvnw spring-boot:run

   # Option B: Using shell
   set -a && source .env && set +a && ./mvnw spring-boot:run
   ```

> **Important:** `.env` is gitignored. Never commit it to version control.
> See [docs/MONITORING.md](docs/MONITORING.md) for more details.

### Q: How to contribute code?

A: Pull Requests are welcome! Please ensure:
- Code passes existing tests
- Follows existing code style
- Updates relevant documentation

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

### Q: How does the CI/CD pipeline work?

A: See [docs/CICD.md](docs/CICD.md) for complete pipeline documentation.

## Related Resources

- [Spring AI Official Documentation](https://docs.spring.io/spring-ai/reference/)
- [Spring AI Agent Utils](https://github.com/spring-ai-community/spring-ai-agent-utils)
- [SkillsJars](https://www.skillsjars.com)
- [Anthropic Skills](https://github.com/anthropics/skills)
- [CI/CD Guide](docs/CICD.md)
- [Contributing Guidelines](CONTRIBUTING.md)
- [Security Policy](SECURITY.md)
- [Pre-Launch Checklist](docs/PRE_LAUNCH_CHECKLIST.md)
- [Release Runbook](docs/RELEASE_RUNBOOK.md)
- [Monitoring Guide](docs/MONITORING.md)

## License

[Apache License 2.0](LICENSE)
