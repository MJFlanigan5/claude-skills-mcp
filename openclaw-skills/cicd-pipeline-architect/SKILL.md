---
name: cicd-pipeline-architect
description: Generates production-grade CI/CD pipelines for GitHub Actions, GitLab CI, and CircleCI
version: 1.0.0
user-invocable: true
metadata:
  clawdbot:
    requires:
      bins:
        - git
---

# CI/CD Pipeline Architect

## Description
Generates production-grade CI/CD pipelines for GitHub Actions, GitLab CI, and CircleCI. Handles deployment configs, environment management, rollback procedures, caching strategies, and security scanning stages. From monorepo to microservices.

## Prerequisites
- OpenClaw installed and running
- Target repository with a clear project structure (monorepo or polyrepo)
- Basic understanding of your deployment target (AWS, GCP, Azure, Vercel, bare metal, etc.)
- CI/CD platform account (GitHub, GitLab, or CircleCI)

## Setup
1. Install this `SKILL.md` in your OpenClaw skills directory.
2. Ensure your project root is accessible to OpenClaw (the skill reads your repo structure to generate accurate pipelines).
3. Reload OpenClaw and confirm this skill is available.

## Commands
- "Generate a GitHub Actions pipeline for my Node.js monorepo with staging and production environments"
- "Create a GitLab CI config with Docker build, security scanning, and blue-green deployment to AWS ECS"
- "Add a rollback workflow to my existing GitHub Actions pipeline"
- "Set up a CircleCI pipeline with parallelized tests, caching, and deploy-on-tag for my Python microservices"
- "Audit my current CI/CD config for performance bottlenecks and missing security stages"

## Capabilities
1. **Multi-platform pipeline generation** — Produces valid YAML configs for GitHub Actions, GitLab CI, and CircleCI with platform-specific best practices (reusable workflows, includes, orbs).
2. **Environment matrix management** — Generates separate stages for dev, staging, and production with proper environment variable isolation, approval gates, and promotion rules.
3. **Caching strategies** — Configures dependency caching (npm, pip, Maven, Go modules, Docker layers) with correct cache keys and fallback paths to minimize cold-start build times.
4. **Security scanning integration** — Adds SAST, dependency vulnerability scanning, container image scanning, and secret detection stages using tools like Trivy, Snyk, CodeQL, and Gitleaks.
5. **Monorepo path filtering** — Generates conditional triggers so only affected services rebuild. Supports Turborepo, Nx, and manual path-based filtering.
6. **Rollback procedures** — Creates explicit rollback workflows and jobs that revert to the last known-good deployment, including database migration rollback hooks.
7. **Multi-stage Docker builds in CI** — Generates build-push workflows with multi-stage Dockerfiles, buildx cross-compilation, and layer caching for registry pushes (ECR, GCR, Docker Hub, GHCR).
8. **Deployment patterns** — Supports blue-green, canary, rolling update, and recreate strategies with platform-specific implementation (Kubernetes, ECS, Cloud Run, Lambda, Vercel).
9. **Notification and observability hooks** — Adds Slack, Discord, or email notifications on failure/success and optional deployment tracking via Datadog, Sentry, or custom webhooks.
10. **Matrix and parallel test execution** — Splits test suites across parallel runners with proper artifact collection and test result aggregation.

## Workflow
1. **Analyze project** — Scan the repository structure, detect languages, frameworks, build tools, and existing CI configs.
2. **Gather requirements** — Confirm target CI platform, deployment targets, environment strategy, and any compliance or security requirements.
3. **Generate pipeline** — Produce the full pipeline config with all stages (lint, test, build, scan, deploy) and environment-specific overrides.
4. **Add supporting configs** — Generate any required supplementary files: Dockerfiles, deployment manifests, environment variable templates, or scripts referenced by the pipeline.
5. **Validate and explain** — Walk through each stage, explain trigger conditions, and flag any manual setup required (secrets, tokens, runner registration).

## Anti-Patterns
- Don't use this to manage runtime infrastructure (Terraform, Pulumi, CDK) — this skill generates CI/CD pipeline configs, not IaC. Use a dedicated infrastructure skill for provisioning.
- Don't use this as a replacement for understanding your deployment target. The skill generates correct configs, but you need to know whether your app belongs on Kubernetes vs. serverless vs. a VM.
- This skill does NOT execute pipelines or deploy code. It generates configuration files. You still push to your repo and let the CI platform run them.
- Don't ask it to generate pipelines for platforms it doesn't support (Jenkins, Bamboo, TeamCity). Stick to GitHub Actions, GitLab CI, and CircleCI.
- Don't skip the environment variable setup. The generated configs reference secrets (like `AWS_ACCESS_KEY_ID` or `DOCKER_PASSWORD`) that you must configure in your CI platform's secret store.

## Examples

### Example 1: Full GitHub Actions Pipeline for a Node.js API
```
Generate a GitHub Actions pipeline for my Express API. I need: lint with ESLint,
test with Jest (parallel across 3 runners), build a Docker image, push to ECR,
and deploy to ECS with staging on push to main and production on release tags.
Include Trivy scanning and Slack notifications on failure.
```
Expected output: A `.github/workflows/ci-cd.yml` file with jobs for lint, test (matrix strategy with 3 shards), docker-build-and-push, trivy-scan, deploy-staging, deploy-production (with environment protection rules), and a Slack notification job on failure. Includes cache configuration for node_modules and Docker layers.

### Example 2: GitLab CI Monorepo with Path Filtering
```
I have a monorepo with services/api, services/worker, and packages/shared. Generate
a GitLab CI config that only builds and deploys the services that changed. Each service
has its own Dockerfile. Deploy to Kubernetes using Helm.
```
Expected output: A `.gitlab-ci.yml` with `rules:changes` path filtering for each service, shared stages for lint and test, per-service Docker build and Helm deploy jobs, and a shared library include for common configuration. Includes Kubernetes context setup and Helm value overrides per environment.

### Example 3: Rollback Workflow
```
Add a manual rollback workflow to my existing GitHub Actions setup. It should let me
pick a previous deployment by tag, revert the ECS service to that image, and run a
health check before marking it stable.
```
Expected output: A `.github/workflows/rollback.yml` with `workflow_dispatch` trigger, an input for the target tag, jobs to pull the previous image digest, update the ECS task definition, wait for service stability, run a health check HTTP request, and post results to Slack.

## Version
v1.0 — Initial release
