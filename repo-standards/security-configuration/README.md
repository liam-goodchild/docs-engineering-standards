# Repository Security Configuration Standards

## Purpose

This document defines the standard for GitHub repository security settings.
The goal is to ensure all repositories have consistent security monitoring and vulnerability detection enabled.

This standard:

- Enables proactive vulnerability detection across all repositories
- Provides automated dependency security updates
- Detects secrets accidentally committed to repositories
- Maintains visibility of security issues through centralised alerting

---

## Required Security Features

All repositories must have the following security features enabled:

| Feature                | Description                                                 |
| ---------------------- | ----------------------------------------------------------- |
| Security Advisories    | Track and manage security vulnerabilities in the repository |
| Dependabot Alerts      | Automatic alerts for vulnerable dependencies                |
| Code Scanning Alerts   | Static analysis to detect security vulnerabilities in code  |
| Secret Scanning Alerts | Detection of accidentally committed secrets and credentials |

---

## Configuration

Security features are configured in the GitHub repository settings:

1. Navigate to the repository **Settings**
2. Select **Security** from the sidebar
3. Under the **Overview** tab, enable all security features

### Dependabot

Dependabot should be configured to:

- Alert on vulnerable dependencies
- Create automatic pull requests for security updates
- Monitor all supported package ecosystems in the repository

### Code Scanning

Code scanning should be configured with:

- Default CodeQL analysis for supported languages
- Scans triggered on push to default branch
- Scans triggered on pull requests

### Secret Scanning

Secret scanning should be configured to:

- Scan for supported secret patterns
- Alert repository administrators on detection
- Block pushes containing detected secrets (push protection)

---

## Verification

To verify security configuration is complete:

1. Navigate to the repository **Security** tab
2. Check the **Overview** section shows all features enabled
3. Verify no configuration warnings are displayed

---

## Summary

All repositories must have security advisories, Dependabot alerts, code scanning, and secret scanning enabled. These settings are configured in the Security blade of the repository settings and provide automated vulnerability detection and alerting.
