# CD Pipeline Standards

## Purpose

This document defines the standard for Continuous Deployment (CD) pipelines.
The goal is to provide a consistent, repeatable approach to deploying infrastructure using Terraform with proper state management, environment separation, and versioning.

This standard:

- Ensures consistent deployment patterns across all infrastructure repositories
- Provides environment-specific configuration management
- Enables automated Git versioning based on commit messages
- Leverages shared pipeline templates for maintainability

---

## Example Pipelines

Example CD pipelines are provided [here](https://github.com/liam-goodchild/pipeline-engineering-templates/blob/main/_examples).

---

## Pipeline Overview

The standard CD pipeline consists of three stages:

1. **Terraform Plan** - Generates an execution plan showing proposed infrastructure changes
2. **Terraform Apply** - Applies the planned changes to the target environment
3. **Git Versioning** - Creates a Git tag based on commit message or branch name (optional)

---

## Template Pipeline

```yaml
trigger:
  branches:
    include:
      - main
  paths:
    exclude:
      - "**/README.md"

pr: none

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
    default: " "
  - name: enableVersioning
    displayName: Enable Git Versioning
    type: boolean
    default: true

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
  # Stage 1: Terraform Plan
  - stage: Plan
    displayName: "Terraform Plan"
    jobs:
      - job: TerraformPlan
        displayName: "Terraform Plan"
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
              publishPlan: true
              planArtifactName: "tfplan-${{ parameters.environment }}"
              ensureBackendContainer: true

  # Stage 2: Terraform Apply
  - stage: Apply
    displayName: "Terraform Apply"
    dependsOn: Plan
    condition: succeeded()
    jobs:
      - job: TerraformApply
        displayName: "Terraform Apply"
        steps:
          - checkout: self
          - template: terraform/terraform-cicd.yaml@templates
            parameters:
              terraformAction: apply
              serviceConnectionName: $(serviceConnectionName)
              backendResourceGroupName: $(backendResourceGroupName)
              backendStorageAccountName: $(backendStorageAccountName)
              backendContainerName: $(backendContainerName)
              backendKey: terraform.tfstate
              envTfvars: $(tfvars)
              globalTfvars: $(globalTfvars)
              additionalCommandOptions: ${{ parameters.additionalTfVars }}
              ensureBackendContainer: false

  # Stage 3: Git Versioning
  - stage: Versioning
    displayName: "Git Versioning"
    dependsOn: Apply
    condition: and(succeeded(), eq('${{ parameters.enableVersioning }}', 'true'))
    jobs:
      - job: GitVersioning
        displayName: "Create Git Tag"
        steps:
          - template: versioning/git-versioning.yaml@templates
            parameters:
              defaultAction: patch
              defaultBranch: main
```

---

## Configuration

### Triggers

| Setting        | Value          | Description                                |
| -------------- | -------------- | ------------------------------------------ |
| Branch trigger | `main`         | Pipeline runs on commits to main branch    |
| Path exclusion | `**/README.md` | Readme changes do not trigger deployments  |
| PR trigger     | `none`         | Pull requests do not trigger this pipeline |

### Parameters

| Parameter          | Type    | Default | Description                                    |
| ------------------ | ------- | ------- | ---------------------------------------------- |
| `environment`      | string  | -       | Target environment (Dev or Prod)               |
| `additionalTfVars` | string  | `' '`   | Additional Terraform variables to pass         |
| `enableVersioning` | boolean | `true`  | Enable Git tag creation after successful apply |

### Variables to Configure

The following placeholders must be replaced with repository-specific values:

| Variable                | Description                                    |
| ----------------------- | ---------------------------------------------- |
| `backendContainerName`  | Storage container name for Terraform state     |
| `serviceConnectionName` | Azure DevOps service connection for deployment |

### Environment-Specific Variables

State management variables are automatically configured based on the selected environment:

| Environment | Resource Group             | Storage Account      | Environment Code |
| ----------- | -------------------------- | -------------------- | ---------------- |
| Dev         | `sh-mgmt-dev-uks-tf-rg-01` | `shmgmtdevukstfst01` | `dev`            |
| Prod        | `sh-mgmt-prd-uks-tf-rg-01` | `shmgmtprdukstfst01` | `prd`            |

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

1. Copy the template pipeline to your repository as `azure-pipelines-cd.yaml`
2. Replace `PLACEHOLDER` values with your repository-specific configuration
3. Ensure your tfvars files follow the expected structure
4. Configure the appropriate service connection in Azure DevOps

---

## Git Versioning

When enabled, the versioning stage creates Git tags based on:

- Commit message keywords (major, minor, patch)
- Branch name patterns
- Default action: `patch` increment

---

## Summary

CD pipelines should follow this standard template to ensure consistent deployments across all infrastructure repositories. The three-stage approach (Plan, Apply, Version) provides visibility into changes before they are applied and maintains a clear version history of deployments.
