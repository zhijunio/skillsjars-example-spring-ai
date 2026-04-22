# Release Runbook

This document provides step-by-step instructions for releasing a new version.

## Release Types

### Snapshot Release (Automatic)

Snapshot versions are automatically published on every push to `main`.

**Trigger:** Push to `main` branch  
**Workflow:** `.github/workflows/publish-snapshot.yml`  
**Version Format:** `0.0.1-SNAPSHOT`

### Official Release (Manual)

Official releases are published to Maven Central with semantic versioning.

**Trigger:** Manual workflow dispatch  
**Workflow:** `.github/workflows/release.yml`  
**Version Format:** `0.1.0`, `1.0.0`, etc.

---

## Official Release Steps

### Pre-Release (1-2 days before)

1. **Verify all tests pass**
   ```bash
   ./mvnw clean verify
   ```

2. **Check for security vulnerabilities**
   ```bash
   ./mvnw dependency-check:check
   ```

3. **Update CHANGELOG.md**
   - Add all changes under "Unreleased" section
   - Ensure version and date will be added during release

4. **Verify documentation is current**
   - [ ] README.md reflects new features
   - [ ] API documentation is accurate
   - [ ] CICD.md is up to date

5. **Notify team**
   - Announce planned release date
   - Confirm no blocking issues

### Release Day

#### Step 1: Final Checks (30 minutes)

```bash
# Pull latest changes
git pull origin main

# Run full build
./mvnw clean verify -B

# Check CI status
# Visit: https://github.com/zhijunio/skillsjars-example-spring-ai/actions
```

#### Step 2: Create Release (15 minutes)

1. Go to **Actions** tab on GitHub

2. Select **"Release"** workflow

3. Click **"Run workflow"**

4. Fill in inputs:
   - **version**: `0.1.0` (follow semver)
   - **createTag**: `true` (check the box)

5. Click **"Run workflow"**

#### Step 3: Monitor Release (10 minutes)

Watch the workflow execution:

- ✅ Build succeeds
- ✅ Tests pass
- ✅ Artifacts upload to Maven Central
- ✅ Git tag created
- ✅ GitHub Release created
- ✅ Version bumped to next SNAPSHOT

**If any step fails:**
- Check workflow logs for error
- Follow rollback procedure below
- Fix issue and retry

#### Step 4: Verify Release (15 minutes)

1. **Check Maven Central**
   ```
   https://central.sonatype.com/artifact/io.zhijun/skillsjars-example-spring-ai
   ```

2. **Verify GitHub Release**
   ```
   https://github.com/zhijunio/skillsjars-example-spring-ai/releases
   ```

3. **Verify Git tag**
   ```bash
   git tag -l
   git show v0.1.0
   ```

4. **Test installation**
   ```bash
   # In a new project, add dependency and verify it resolves
   ```

#### Step 5: Announce Release

1. **Update project README**
   - Update version badges if applicable
   - Add release highlights

2. **Notify stakeholders**
   - Team Slack/Teams channel
   - Email distribution list (if applicable)
   - Social media (if applicable)

3. **Update documentation**
   - Release notes in project website (if applicable)

---

## Rollback Procedure

### If Release to Maven Central Fails

1. **Check Maven Central for partial artifacts**
   - Visit https://central.sonatype.com
   - Search for your artifact

2. **If artifacts exist but release is incomplete:**
   - Contact Sonatype support: support@sonatype.com
   - Request removal of partial release
   - Reference: `io.zhijun:skillsjars-example-spring-ai:0.1.0`

3. **Clean up Git tags**
   ```bash
   # Delete local tag
   git tag -d v0.1.0

   # Delete remote tag
   git push origin :refs/tags/v0.1.0

   # Verify deletion
   git tag -l
   ```

4. **Fix the issue**
   - Review workflow logs
   - Fix configuration or code issue
   - Test locally

5. **Retry release**
   - Run release workflow again
   - Use same or new version number

### If Post-Release Issues Discovered

1. **Assess severity**
   - Critical (security, data loss): Immediate rollback
   - High (major functionality): Rollback within 24 hours
   - Medium (minor issues): Fix in patch release

2. **For critical issues, revert code**
   ```bash
   git revert <release-commit-hash>
   git push origin main
   ```

3. **Release emergency patch**
   - Follow release steps
   - Use patch version: `0.1.1`

---

## Post-Release (Day After)

1. **Monitor for issues** (first 24-48 hours)
   - GitHub Issues
   - User reports
   - Error monitoring (if applicable)

2. **Update internal documentation**
   - Feature availability
   - Support documentation

3. **Celebrate!** 🎉
   - Acknowledge team effort
   - Document lessons learned

---

## Release Calendar

| Version | Planned Date | Status | Owner |
|---------|--------------|--------|-------|
| 0.1.0 | TBD | Planned | |
| 0.2.0 | TBD | Planned | |

---

## Contacts

| Role | Contact |
|------|---------|
| Release Manager | zhijunio |
| Sonatype Support | support@sonatype.com |

---

## Troubleshooting

### GPG Signing Fails

**Error:** `gpg: signing failed: No secret key`

**Solution:**
1. Verify `GPG_SECRET_KEY` secret contains armored private key
2. Verify `GPG_PASSPHRASE` matches the key passphrase
3. Regenerate armored key if needed:
   ```bash
   gpg --armor --export-secret-keys your-key-id
   ```

### Maven Central Publishing Fails

**Error:** `401 Unauthorized` or `403 Forbidden`

**Solution:**
1. Verify `MAVEN_USERNAME` and `MAVEN_PASSWORD` secrets
2. Check Sonatype account status
3. Ensure GPG key is uploaded to key server

### Workflow Fails at "Bump Version" Step

**Symptom:** Release succeeded but version bump failed

**Solution:**
1. Manually update `pom.xml` to next SNAPSHOT version
2. Commit and push:
   ```bash
   git add pom.xml
   git commit -m "chore: bump to next development version"
   git push
   ```

---

## Checklist Summary

```
Pre-Release:
[ ] All tests pass
[ ] Security check passes
[ ] CHANGELOG.md updated
[ ] Documentation current
[ ] Team notified

Release Day:
[ ] Final verification complete
[ ] Release workflow triggered
[ ] Workflow completed successfully
[ ] Maven Central artifact verified
[ ] GitHub Release created
[ ] Git tag created
[ ] Version bumped to next SNAPSHOT
[ ] Announcement sent

Post-Release:
[ ] Monitor for issues (24-48 hours)
[ ] Update internal docs
[ ] Lessons learned documented
```
