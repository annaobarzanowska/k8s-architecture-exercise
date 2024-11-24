# Company blog - scalable, on AWS EKS using Gitops

This architecture leverages EKS for container orchestration, S3 for static content hosting, and Flux for GitOps deployment, creating a secure, scalable, and maintainable company blog.

## Components:

### Content Repository:

Git Repository: Stores blog content (Markdown, HTML, images) under version control.

### CI/CD Pipeline:

Trigger: CI/CD pipeline triggered on Git push events, such as Github Actions / GitlabCI

#### Build and Deploy:

- Builds content (e.g., generating static HTML from Markdown if necessary).
- Deploys built content to an S3 bucket.
- Updates the Podinfo deployment configuration to point to the latest S3 version.

### EKS Cluster:

- Podinfo Deployment: Podinfo containerized and deployed as a web server.
- Service: Exposes the Podinfo service within the cluster.
- Ingress Controller: Routes external traffic to the Podinfo service.
- Horizontal Pod Autoscaler (HPA): Automatically scales Podinfo pods based on traffic.

### S3 Bucket:

- Static Content Storage: Stores built blog content with versioning enabled.
- Static Website Hosting: Configured for optimal content delivery.
- Access Control: Grants appropriate access permissions.

### GitOps with Flux:

- Flux Configuration Repository: Stores desired state configurations (e.g., Kubernetes manifests, S3 bucket configuration) in a Git repository.
- Flux Controller: Runs in the EKS cluster, monitoring the Git repository for changes and applying them automatically.

## Justification of Choices:

Podinfo: Simplified web server for serving S3 content.
EKS: Managed Kubernetes service on AWS for container orchestration.
S3: Highly scalable and cost-effective object storage for static content.
Flux: Enables GitOps approach for declarative infrastructure management and automated deployments.

## Security Considerations:

Secure Git repository with access control.
Implement IAM roles and policies for Podinfo and EKS resources.
Configure network policies to restrict communication within the cluster.
Configure S3 bucket access permissions appropriately.

## Scalability:

HPA automatically scales Podinfo pods based on resource utilization (e.g., CPU usage).
S3 scales horizontally to accommodate increased traffic.

## Maintainability:

Infrastructure as Code (IaC) with manifests stored in the Git repository.
GitOps simplifies configuration management and deployment updates.

## Monitoring & Observability:

Monitor Podinfo pod health and resource usage.
Consider Prometheus and Grafana for deeper metrics insights.
Utilize CloudWatch for monitoring EKS cluster health and resource utilization.

## Cost-Effectiveness:

Utilize pay-as-you-go models for EKS and S3 based on actual resource usage.
Choose cost-optimized instance types for worker nodes.

## Deployment Steps:

Create an EKS cluster on AWS.
Set up a VPC and configure security groups.
Configure S3 bucket for static website hosting and access control.
Implement CI/CD pipeline to build, deploy content to S3, and update Podinfo configuration.
Define Kubernetes manifests for Podinfo deployment, service, and (optional) HPA.
Configure Flux with a Git repository containing manifests and S3 bucket configurations.
Deploy Podinfo and configure it to serve content from the S3 bucket endpoint.
