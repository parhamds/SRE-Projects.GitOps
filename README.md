# Project Overview: GitOps

## Summary
This GitOps project is a streamlined workflow for managing infrastructure and application changes using GitOps principles. It involves automating the deployment and management of infrastructure and applications on AWS EKS (Elastic Kubernetes Service) and managing Infrastructure as Code (IaC) alongside application code.

## Components

- **Infrastructure Phase**:
  - Infrastructure changes involve writing or modifying Terraform code, pushing to GitHub, and applying infrastructure changes automatically using GitHub Actions on push to the main branch.
  
- **Application Phase**:
  - Application changes include modifying application code, pushing to GitHub, where GitHub Actions automate building and testing the code. Docker images are created, pushed to Amazon ECR (Elastic Container Registry), and deployed to AWS EKS using Helm, which inserts the latest Docker image and tag as Helm variables.

## Setup Steps
1. **S3 Bucket**: Create an S3 bucket for storing Terraform state files.
2. **IAM User**: Create an IAM user with admin access and generate access keys.
3. **ECR Repository**: Create an Amazon ECR repository for storing Docker images.
4. **GitHub Repositories**: Create two GitHub repositories for IaC code and application code, each containing staging and main branches.
  
## Infrastructure Workflow
- **IaC GitHub Repository**:
  - Add access keys and S3 bucket name to GitHub secrets.
- **Application GitHub Repository**:
  - Add access keys and ECR repository address to GitHub secrets.
- **Terraform Code**:
  - Utilizes VPC and EKS modules for infrastructure provisioning.
- **Staging Workflow**:
  - Triggered on push to staging or main branch or pull requests on the main branch with changes in the Terraform folder. Executes Terraform initialization, formatting, validation, planning, and saves the plan output to a file. Checks the Terraform plan result.
- **Main Workflow**:
  - Continuation of the staging workflow. Applies Terraform changes, configures AWS CLI, configures kubectl using EKS CLI, and applies Kubernetes commands (e.g., deploying Nginx Ingress).

## Application Workflow
- **SonarCloud Integration**:
  - Create SonarCloud organization, project, token, and quality gate. Add the information to GitHub repository secrets.
- **Workflow Jobs**:
  - **Testing**: Run tests on Ubuntu, checkout source code, create the application using Maven, set up Java 11, run SonarQube scanner, and check the Sonar quality gate.
  - **Build and Publish**: Build Docker image and upload it to ECR using a GitHub Marketplace action.
  - **Deploy to EKS**: Configure AWS CLI, configure kubectl using EKS CLI, create a Kubernetes secret to log in to ECR, set up Helm, fetch image from ECR, and deploy on EKS using a GitHub Actions deploy EKS Helm chart.

## Helm Template
- Create a Helm template.
- Copy Kubernetes resources into the template folder.
- Replace Docker image and tag of the deployment with Helm variables.