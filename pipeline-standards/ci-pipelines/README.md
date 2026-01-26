# CI Pipeline Standards

## Purpose

This document defines the standard for Continuous Integration (CI) pipelines.
The goal is to provide a consistent approach to validating infrastructure changes through linting, documentation generation, and plan verification before merging.

This standard:
- Ensures code quality through automated linting and security scanning
- Maintains up-to-date Terraform documentation automatically
- Validates infrastructure changes with Terraform Plan before merge
- Leverages shared pipeline templates for maintainability

---

## Example Pipelines

Example CI pipelines are provided [here](https://github.com/liam-goodchild/pipeline-engineering-templates/blob/main/_examples/ci-terraform.yaml).

---

## Pipeline Overview

The standard CI pipeline consists of three stages:

1. **Linting & Validation** - Runs Super-Linter with Prettier formatting and Checkov security scanning
2. **Documentation** - Generates and commits Terraform documentation automatically
3. **Terraform Plan** - Validates proposed infrastructure changes without applying

---

## Template Pipeline

```yaml
trigger: none

pr:
  branches:
    include:
      - main
  paths:
    exclude:
      - '**/README.md'

resources:
  repositories:
    - repository: templates
      type: github
      name: liam-goodchild/pipeline-engineering-templates
      endpoint: liam-goodchild
      ref: refs/heads/main

pool:
  vmImage: ubuntu-latest

parameters:
  - name: environment
    displayName: Environment
    type: string
    values: [Dev, Prod]
  - name: additionalTfVars
    displayName: Additional Terraform Variables
    type: string
    default: ' '

variables:
  # Container, Service Connection & tfvars
  backendContainerName: PLACEHOLDER
  serviceConnectionName: PLACEHOLDER
  globalTfvars: infra/vars/globals.tfvars
  tfvars: infra/vars/uks/$(environmentCode).tfvars
  # State Management
  ${{ if eq(parameters.environment, 'Prod') }}:
    backendResourceGroupName: sh-mgmt-prd-uks-tf-rg-01
    backendStorageAccountName: shmgmtprdukstfst01
    environmentCode: prd
  ${{ else }}:
    backendResourceGroupName: sh-mgmt-dev-uks-tf-rg-01
    backendStorageAccountName: shmgmtdevukstfst01
    environmentCode: dev

stages:
  # Stage 1: Linting
  - stage: Linting
    displayName: 'Linting & Validation'
    jobs:
      - job: SuperLinter
        displayName: 'Super-Linter'
        steps:
          - template: validation/linting.yaml@templates
            parameters:
              prettierConfigFile: .azuredevops/linters/.prettierrc.json
              checkovConfigFile: .azuredevops/linters/.checkov.yaml

  # Stage 2: Documentation
  - stage: Documentation
    displayName: 'Terraform Documentation'
    dependsOn: Linting
    condition: succeeded()
    jobs:
      - job: TerraformDocs
        displayName: 'Generate Terraform Docs'
        steps:
          - template: documentation/tf-documentation.yaml@templates
            parameters:
              terraformDir: infra
              targetBranch: $(System.PullRequest.SourceBranch)

  # Stage 3: Terraform Plan
  - stage: Plan
    displayName: 'Terraform Plan'
    dependsOn: Documentation
    condition: succeeded()
    jobs:
      - job: TerraformPlan
        displayName: 'Terraform Plan'
        steps:
          - checkout: self
          - template: terraform/terraform-cicd.yaml@templates
            parameters:
              terraformAction: plan
              serviceConnectionName: $(serviceConnectionName)
              backendResourceGroupName: $(backendResourceGroupName)
              backendStorageAccountName: $(backendStorageAccountName)
              backendContainerName: $(backendContainerName)
              backendKey: terraform.tfstate
              envTfvars: $(tfvars)
              globalTfvars: $(globalTfvars)
              additionalCommandOptions: ${{ parameters.additionalTfVars }}
              publishPlan: false
              ensureBackendContainer: true
```

---

## Configuration

### Triggers

| Setting | Value | Description |
|---------|-------|-------------|
| Branch trigger | `none` | Pipeline does not run on direct commits |
| PR trigger | `main` | Pipeline runs on pull requests to main |
| Path exclusion | `**/README.md` | README changes do not trigger validation |

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `environment` | string | - | Target environment for plan validation (Dev or Prod) |
| `additionalTfVars` | string | `' '` | Additional Terraform variables to pass |

### Variables to Configure

The following placeholders must be replaced with repository-specific values:

| Variable | Description |
|----------|-------------|
| `backendContainerName` | Storage container name for Terraform state |
| `serviceConnectionName` | Azure DevOps service connection for plan execution |

### Environment-Specific Variables

State management variables are automatically configured based on the selected environment:

| Environment | Resource Group | Storage Account | Environment Code |
|-------------|----------------|-----------------|------------------|
| Dev | `sh-mgmt-dev-uks-tf-rg-01` | `shmgmtdevukstfst01` | `dev` |
| Prod | `sh-mgmt-prd-uks-tf-rg-01` | `shmgmtprdukstfst01` | `prd` |

---

## Linter Configuration

The pipeline expects linter configuration files in the following structure:

```
.azuredevops/
└── linters/
    ├── .prettierrc.json    # Prettier formatting rules
    └── .checkov.yaml       # Checkov security scan configuration
```

---

## Tfvars Structure

The pipeline expects the following tfvars file structure:

```
infra/
└── vars/
    ├── globals.tfvars          # Shared variables across environments
    └── uks/
        ├── dev.tfvars          # Dev environment variables
        └── prd.tfvars          # Prod environment variables
```

---

## Usage

1. Copy the template pipeline to your repository as `azure-pipelines-ci.yaml`
2. Replace `PLACEHOLDER` values with your repository-specific configuration
3. Create linter configuration files in `.azuredevops/linters/`
4. Ensure your tfvars files follow the expected structure
5. Configure the appropriate service connection in Azure DevOps

---

## CI vs CD Pipeline Comparison

| Aspect | CI Pipeline | CD Pipeline |
|--------|-------------|-------------|
| Trigger | Pull requests | Main branch commits |
| Linting | Yes | No |
| Documentation | Yes | No |
| Terraform Plan | Yes | Yes |
| Terraform Apply | No | Yes |
| Git Versioning | No | Yes (optional) |

---

## Summary

CI pipelines should follow this standard template to ensure consistent validation of infrastructure changes before merge. The three-stage approach (Lint, Document, Plan) catches issues early in the development cycle and maintains documentation automatically.
