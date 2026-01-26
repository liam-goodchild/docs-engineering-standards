# Branch Policy Standards

## Purpose

This document defines the standard for GitHub branch protection rulesets.
The goal is to ensure consistent branch protection across all repositories to maintain code quality and prevent accidental changes to protected branches.

This standard:
- Protects the default branch from direct pushes and force pushes
- Enforces pull request workflows for all changes
- Prevents accidental branch deletion
- Provides a consistent, importable ruleset for new repositories

---

## Prerequisites

Before importing the branch ruleset, ensure:

1. The repository has been created
2. The `main` branch exists and is set as the default branch
3. You have admin permissions on the repository

---

## Ruleset Overview

The standard ruleset (`main-branch-protection.json`) applies the following protections to the default branch:

| Rule | Description |
|------|-------------|
| Deletion | Prevents the default branch from being deleted |
| Non-fast-forward | Prevents force pushes to the default branch |
| Pull request | Requires all changes to come through a pull request |

### Pull Request Settings

| Setting | Value | Description |
|---------|-------|-------------|
| Required approving reviews | `0` | Minimum approvals before merge (adjust per repository) |
| Dismiss stale reviews | `false` | Reviews are not dismissed when new commits are pushed |
| Require code owner review | `false` | Code owner approval not required |
| Require last push approval | `false` | Last pusher can approve their own PR |
| Require review thread resolution | `false` | Unresolved conversations do not block merge |
| Allowed merge methods | `merge`, `squash`, `rebase` | All merge strategies permitted |

---

## Importing the Ruleset

### Using GitHub CLI

```bash
gh api repos/{owner}/{repo}/rulesets --method POST --input main-branch-protection.json
```

### Using GitHub UI

1. Navigate to the repository **Settings**
2. Select **Rules** > **Rulesets** from the sidebar
3. Click **New ruleset** > **Import a ruleset**
4. Upload `main-branch-protection.json`
5. Review the imported rules and click **Create**

---

## Customisation

After importing, you may need to adjust settings based on repository requirements:

| Setting | When to Adjust |
|---------|----------------|
| `required_approving_review_count` | Increase for production or critical repositories |
| `dismiss_stale_reviews_on_push` | Enable for repositories requiring fresh reviews |
| `require_code_owner_review` | Enable when CODEOWNERS file is configured |
| `bypass_actors` | Add users or teams that need emergency access |

---

## Ruleset File

The ruleset is defined in [main-branch-protection.json](main-branch-protection.json):

```json
{
  "name": "main-branch-protection",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": {
      "include": ["~DEFAULT_BRANCH"]
    }
  },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 0,
        "allowed_merge_methods": ["merge", "squash", "rebase"]
      }
    }
  ]
}
```

---

## Summary

All new repositories must import the standard branch protection ruleset after setting main as the default branch. This ensures consistent protection across the organisation and prevents accidental modifications to protected branches.
