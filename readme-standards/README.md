# Readme Standards

## Purpose

This document defines the standard format for readme files across all repositories.
The goal is to ensure consistency, readability, and completeness across all documentation.

This standard:
- Provides a consistent structure for all repository documentation
- Ensures key information is always present and easy to find
- Makes documentation maintainable and predictable
- Improves onboarding by providing familiar patterns

---

## Required Sections

Every readme must include the following sections in this order:

### 1. Title

```markdown
# [Topic] Standards
```

- Use title case
- End with "Standards" for consistency
- Be concise and descriptive

### 2. Purpose

```markdown
## Purpose

This document defines the standard for [topic].
The goal is to [primary objective].

This standard:
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]
```

- Explain what the document covers
- State the primary goal
- List 3-5 key benefits as bullet points

### 3. Main Content Sections

```markdown
---

## [Section Name]

[Content]
```

- Use horizontal rules (`---`) to separate major sections
- Use descriptive section headings
- Break complex topics into subsections using `###`

### 4. Summary

```markdown
---

## Summary

[1-2 sentences summarising the key points]
```

- Always include as the final section
- Keep brief and actionable

---

## Optional Sections

Include these sections when relevant:

| Section | When to Include |
|---------|-----------------|
| Examples | When practical demonstrations aid understanding |
| Configuration | When settings or parameters need documentation |
| Usage | When step-by-step instructions are needed |
| Migration Guidance | When transitioning from old patterns |
| Governance | When compliance or approval processes apply |
| Quick Reference | When a summary table aids quick lookup |

---

## Formatting Rules

### General

- Use GitHub-flavoured Markdown
- Use horizontal rules (`---`) between major sections
- Keep line lengths reasonable for readability
- Use empty lines to separate paragraphs

### Headings

- `#` for document title (one per document)
- `##` for major sections
- `###` for subsections
- `####` for sub-subsections (use sparingly)

### Lists

- Use `-` for unordered lists
- Use `1.` for ordered lists
- Maintain consistent indentation (2 spaces)

### Code

- Use fenced code blocks with language identifiers
- Use inline code for filenames, commands, and values

```markdown
Use `backticks` for inline code
```

### Tables

- Use tables for structured data comparison
- Include header row
- Align columns consistently

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Value 1  | Value 2  | Value 3  |
```

### Links

- Use descriptive link text
- Prefer relative links for internal documentation
- Use `[text](url)` format

---

## Template

```markdown
# [Topic] Standards

## Purpose

This document defines the standard for [topic].
The goal is to [primary objective].

This standard:
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

---

## [Main Section 1]

[Content]

---

## [Main Section 2]

[Content]

### [Subsection]

[Content]

---

## Examples

[Practical examples if applicable]

---

## Summary

[Brief summary of key points and guidance]
```

---

## Examples

### Good Title Examples

- Repository Naming Standards
- CD Pipeline Standards
- Terraform State Management Standards
- Secure Values Standards

### Good Purpose Section Example

```markdown
## Purpose

This document defines the standard for Terraform state management.
The goal is to ensure secure, consistent, and recoverable state storage across all infrastructure deployments.

This standard:
- Ensures state files are stored securely in Azure Storage
- Provides environment isolation through separate storage accounts
- Enables state locking to prevent concurrent modifications
- Establishes backup and recovery procedures
```

---

## Summary

All readme files should follow this standard format. Consistency in structure makes documentation easier to navigate, maintain, and understand.
