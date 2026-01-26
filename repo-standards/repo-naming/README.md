# Repository Naming Standards

## Purpose

This document defines the standard for repository naming.
The goal is to provide a consistent, scalable, and provider-agnostic naming convention that remains clear, future-proof, and easy to govern.

This standard:
- Avoids cloud or tooling lock-in
- Works for infrastructure, applications, and documentation
- Scales from small projects to platform estates
- Makes ownership and intent immediately obvious

---

## Naming Format

### Base format (required)

    <repo-type>-<system>-<component>

### Extended format (optional qualifiers)

    <repo-type>-<system>-<component>[-<platform>][-<tool-or-runtime>][-<qualifier>]

Token order is fixed and must not be changed.

---

## Token Definitions

### 1. repo-type (required)
Defines what kind of repository this is.

Approved values:
- app: application code
- service: microservice or background service
- infra: infrastructure root / composition
- module: reusable infrastructure or code modules
- lib: shared libraries
- pipeline: shared CI/CD templates
- policy: policy-as-code
- ops: operational tooling and runbooks
- docs: documentation-only repositories
- solution: multi-layer repos containing app and infra

This value is mandatory and must come first.

---

### 2. system (required)
The product, platform, or domain the repository belongs to.

Examples:
- landingzone
- cvengine
- certwatch
- engineering

This value provides ownership and contextual grouping.

---

### 3. component (required)
The specific part of the system covered by the repository.

Examples:
- core
- management
- networking
- web
- api
- frontend
- standards
- templates

This defines what within the system the repo is responsible for.

---

### 4. platform (optional)
The target platform or environment.
Only include when the repository is explicitly platform-specific or has parallel implementations.

Examples:
- azure
- aws
- gcp
- onprem
- k8s

Avoid including this unless it adds clarity.

---

### 5. tool-or-runtime (optional)
The primary tool or runtime used.

Examples:
- terraform
- bicep
- pulumi
- node
- dotnet
- python

Use only when it differentiates otherwise similar repositories.

---

### 6. qualifier (optional)
A final disambiguator used only when necessary.

Examples:
- standards
- templates
- reference
- poc

Keep short and meaningful.

---

## Formatting Rules

- Use kebab-case only
- Lowercase letters and numbers only
- No underscores, spaces, or camelCase
- Do not include:
  - environments (dev, uat, prod)
  - regions (uks, euw)
  - personal or team names
- Keep names concise (prefer 3-5 tokens before qualifiers)

---

## Examples

### Documentation
- docs-engineering-standards

### Infrastructure
- infra-landingzone-core
- infra-cvengine-portfolio
- infra-landingzone-mgmt-az-tf

### Applications
- app-cvengine-web
- app-cvengine-api-node
- app-certwatch-portal-dotnet

### Reusable Modules
- module-networking-terraform
- module-identity-terraform
- module-naming-terraform

### Pipelines
- pipeline-templates-ci
- pipeline-templates-terraform

### Multi-layer (Solution / Monorepo)
- solution-holiday-suggester-web
- solution-certwatch-portal

---

## When to Use solution

Use solution-* when a repository intentionally contains multiple layers, such as:
- Infrastructure
- Frontend
- Functions or APIs
- Pipelines

This avoids misclassifying complex repositories as purely infra or app.

---

## Migration Guidance

Avoid tool or vendor-first naming.

Examples:
- tf-az-core-infra -> infra-landingzone-core
- tf-az-cvengine-infra -> solution-cvengine-portfolio

Only add platform or tool suffixes when required.

---

## Governance

- All new repositories must follow this standard
- Only approved repo-type values may be used
- Exceptions must be documented in the repository README

---

## Quick Reference

- App code -> app-*
- Infrastructure root -> infra-*
- Reusable module -> module-*
- Docs only -> docs-*
- Pipelines -> pipeline-*
- Multi-layer repo -> solution-*

---

## Summary
Repository names should describe what the repo is, what it belongs to, and what it does without tying identity to a specific cloud provider or tool.
This standard ensures clarity, consistency, and long-term maintainability.
