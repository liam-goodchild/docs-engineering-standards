# Terraform State Management Standards

## Purpose

This document defines the standard for Terraform state storage and management.
The goal is to ensure secure, consistent, and recoverable state storage across all infrastructure deployments.

This standard:

- Centralises state storage in Azure Blob Storage
- Provides environment isolation through separate storage accounts
- Enables automatic state locking to prevent concurrent modifications
- Protects state resources with delete locks

---

## Storage Architecture

Terraform state must be stored in Azure Blob Storage, never locally. Azure Blob handles state locking and leasing automatically.

### Storage Accounts

| Environment | Resource Group             | Storage Account      |
| ----------- | -------------------------- | -------------------- |
| Dev         | `sh-mgmt-dev-uks-tf-rg-01` | `shmgmtdevukstfst01` |
| Prod        | `sh-mgmt-prd-uks-tf-rg-01` | `shmgmtprdukstfst01` |

### Container Naming

- Each project uses a dedicated container
- Container names match the repository name
- CI/CD pipelines create containers automatically if they do not exist

---

## State Locking

Azure Blob Storage provides automatic state locking through blob leases:

- Locks are acquired automatically when Terraform runs
- CI/CD pipeline templates include stages to release locks on unsuccessful runs
- This prevents concurrent modifications and state corruption

---

## Security

### Delete Locks

Delete locks are applied at the resource group level to prevent accidental or malicious tampering with state files.

### Access Control

- RBAC is granted at the container level for service principals
- Each project's service principal only has access to its own container

### Network Access

Storage accounts are currently accessible publicly. Future improvements planned:

| Improvement                                                        | Status  |
| ------------------------------------------------------------------ | ------- |
| Add agent firewall IPs to storage account firewall on pipeline run | Planned |
| Enable blob versioning for state history and recovery              | Planned |

---

## Configuration

### Pipeline Variables

The CI/CD pipelines use the following variables for state management:

| Variable                    | Description                                   |
| --------------------------- | --------------------------------------------- |
| `backendResourceGroupName`  | Resource group containing the storage account |
| `backendStorageAccountName` | Storage account for state files               |
| `backendContainerName`      | Container name (set to repository name)       |
| `backendKey`                | State filename (default: `terraform.tfstate`) |

---

## Summary

Terraform state must be stored in the centralised Azure Blob Storage accounts, with containers named after the repository. Delete locks and automatic lease management protect state integrity, while RBAC at the container level provides access control.
