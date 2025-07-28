# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is Kubestack, an open source Terraform framework for Kubernetes Platform Engineering. It provides convention-over-configuration modules for deploying Kubernetes clusters on AWS (EKS), Azure (AKS), Google Cloud (GKE), and Kind for local development.

## Development Commands

### Building and Testing
- `make dist` - Build distribution packages for quickstart configurations
- `make build` - Build Docker images for the framework
- `make validate` - Run Terraform validation on all configurations
- `make test` - Run comprehensive tests including cluster provisioning
- `make cleanup` - Destroy test infrastructure and stop containers
- `make shell` - Open interactive shell in test container

### Development Workflow
- All builds and tests run in Docker containers to ensure consistent environments
- Tests provision actual cloud resources (requires authentication)
- Test container name includes Git SHA: `test-container-$(GIT_SHA)`

## Architecture

### Provider Structure
Each cloud provider has a consistent module structure:
- `{provider}/cluster/` - User-facing cluster module
- `{provider}/_modules/{service}/` - Internal implementation modules
- `{provider}/cluster-local/` - Local cluster configurations

### Core Components
- `common/metadata/` - Consistent naming and labeling across providers
- `common/configuration/` - Configuration inheritance system
- `quickstart/` - Bootstrap configurations for new projects

### Multi-Environment Support
The framework uses Terraform workspaces for environment separation:
- `ops` workspace for operational infrastructure
- `apps` workspace for application workloads
- Configuration inheritance from base to environment-specific settings

### Naming Convention
Resources follow pattern: `[name_prefix]-[workspace]-[provider_region].[provider_name].[base_domain]`

## Testing

Run tests with: `make test`
- Tests provision clusters on AWS, Azure, and GCP
- Requires cloud provider authentication via environment variables:
  - `KBST_AUTH_AWS`
  - `KBST_AUTH_AZ` 
  - `KBST_AUTH_GCLOUD`

Module-specific tests are in `common/*/tests/` directories and run with `terraform test`.

## Container Images

The project builds specialized Docker images for different providers:
- `eks` - AWS EKS only
- `aks` - Azure AKS only  
- `gke` - Google GKE only
- `kind` - Local Kind clusters
- `multi-cloud` - All providers (default)

Images include provider CLIs, Terraform, kubectl, and kustomize.