# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- CI/CD pipelines with GitHub Actions
  - Main CI build with tests and security audit
  - PR validation with title format checking
  - Automatic SNAPSHOT publishing to Maven Central
  - Manual release workflow with GPG signing
- Quality gates
  - Maven Enforcer for version requirements
  - Checkstyle for code style validation
  - OWASP Dependency Check for security scanning
- Documentation
  - Comprehensive CI/CD guide (`docs/CICD.md`)
  - Contributing guidelines (`CONTRIBUTING.md`)
  - Security policy (`SECURITY.md`)
  - Pre-launch checklist (`docs/PRE_LAUNCH_CHECKLIST.md`)
  - Release runbook (`docs/RELEASE_RUNBOOK.md`)
  - Monitoring guide (`docs/MONITORING.md`)
- GitHub templates
  - Issue templates (bug report, feature request)
  - Pull request template
  - CODEOWNERS configuration
- `.env.example` for environment variable template
- `.editorconfig` for consistent editor settings

### Changed
- **Breaking**: Updated minimum Java version from 17 to 21
- Refactored `Application` class for better separation of concerns
  - Extracted `buildChatClient()` method
  - Extracted `runInteractiveLoop()` method
- Simplified `MyLoggingAdvisor` internal structure
  - Consolidated field declarations
  - Streamlined Builder pattern formatting
- Updated all documentation to English
- Changed Maven Central `autoPublish` from `true` to `false` for safety

### Fixed
- Java version configuration mismatch between `pom.xml` and `.sdkmanrc`
- Security issue where logs could leak sensitive information (API keys, tokens)
- Redundant null checks in `MyLoggingAdvisor.redactSensitiveData()`
- Nested conditionals in `MyLoggingAdvisor.after()` method

### Security
- Added log redaction for API keys, tokens, and other sensitive data
- Added security warning for `ShellTools` arbitrary command execution
- Improved release process with manual confirmation step
- Added OWASP Dependency Check to CI pipeline
- Configured GitHub Secrets for sensitive credentials

### Removed
- Unnecessary local variable assignments in Builder patterns
- Excessive blank lines in class declarations

---

## Version Numbering

- **Major**: Breaking architectural changes or incompatible API modifications
- **Minor**: New features, backward compatible
- **Patch**: Bug fixes and minor improvements

## Release Process

1. Update this file
2. Update version in `pom.xml`
3. Create Git tag
4. Run GitHub Actions Release workflow

---

## Links

- [GitHub Releases](https://github.com/zhijunio/skillsjars-example-spring-ai/releases)
- [Maven Central](https://central.sonatype.com/artifact/io.zhijun/skillsjars-example-spring-ai)
