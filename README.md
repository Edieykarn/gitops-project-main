## GitOps Pipeline for AWS Infrastructure with Terraform

This project establishes a GitOps pipeline for provisioning and managing AWS infrastructure using Terraform. It ensures a structured and automated workflow where infrastructure changes are introduced via pull requests, triggering GitHub Actions workflows for validation, cost estimation, and policy enforcement. Manual approval is required before deployment (terraform apply) or deletion (terraform destroy), adding an extra layer of security and oversight.

The infrastructure includes a Grafana server with essential AWS networking components such as an internet gateway and route table. However, the focus is on building a secure and efficient pipeline with validation mechanisms and safeguards. The pipeline design ensures that the feature branch serves as the source of truth, with the main branch updated only after successful deployment.

Implementation Overview
To maintain code integrity, direct commits to the main branch are disabled. All changes must be made via feature branches and pass automated checks before merging. Backend configuration is managed using AWS S3 for state storage and DynamoDB for state locking, preventing conflicts during updates.

Authentication is handled through OpenID Connect (OIDC), eliminating the need for long-lived AWS credentials. The required IAM role must be created in AWS and added as a GitHub secret (ROLE_TO_ASSUME). A pre-commit hook enforces Terraform formatting (terraform fmt) before commits are made, ensuring code consistency.

GitHub Actions Workflows
Once a pull request is opened, multiple workflows run in parallel to validate the proposed changes:

Infracost estimates cost changes and posts a summary as a pull request comment. An API key must be stored in GitHub secrets (INFRACOST_API_KEY).
Terraform Plan runs formatting, validation, and generates an execution plan, ensuring the changes are well-structured.
Open Policy Agent (OPA) Tests enforce compliance policies, restricting instance types to specific configurations for cost efficiency.
TFLint scans for errors and best practice violations, maintaining code quality.
Manual Deployment and Infrastructure Management
To prevent unintended changes, Terraform deployments and deletions require manual approval. Once validation checks pass, the terraform apply workflow must be triggered manually from GitHub Actions. The same applies for terraform destroy.