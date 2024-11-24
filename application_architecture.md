# Assumptions made

## Cloud Provider

AWS

## Publically Accessible

The app needs to be accessible over the internet via HTTPS

## Container registry

I'll use ECR to store the images

## Stateless application

The app doesn't require persistent storage

## Expertise

The team is familiar with AWS services and basic K8s operations

## Region deployment

The cluster will be deployed in multiple AZs within a single region for high availability

# Architecture diagram

![k8s_architecture_podinfo](k8s-arch-diagram.png)

## Explanation

- Users: clients access web app using web browser
- DNS handled by Route53
- VPC with three private subnets, EKS nodes in these private subnets across multpile AZs
- Load balancer distributes incoming EKS traffic
- Ingree controller withing EKS to manage traffic routing
- EKS cluster:
    - master nodes: managed by AWS EKS, handling control plane
    - worker nodes: multiple nodes across AZs for high availability
- Deployment - the app is deployed as a deployment workload in EKS, managing multiple replicas of app pods
- Service exposes the deployment internally, connecting it to the ingress controller

Not on diagram, but central to architecture:
- HPA scapes the number of pods based on incoming traffic
- Git repo central to deployment process
- Flux for GitOps, managing synchronisation
- Monitoring tools - Prometheus and Grafana for metrics and metrics dashboards, log aggregation via Cloudwatch
- CICD pipeline in Gitlab for automated testing and deployment

## Security components

- RBAC
- Polaris
- Cloudflare

# Detailed explanation

## Access and security
- HTTPS is enforced using TLS certificates managed by AWS Certificate MAnager
- Cloudflare configured to protect against DDOS and other common attacks
- Polaris configured to prevent pods spinning up that don't meet the required configuration / security posture

## Load balancing

- NLB will handle sudden traffic spikes
- Ingress resource defines routing rules to direct traffic to appropriate EKS service

## K8s cluster config

- EKS control plan managed by AWS, ensuring high availability
- Nodes deployed aross at least 3 AZs to ensure availability
- ASGs on each node group to enable scaling
- nodes labeled and tainted for better pod scheduling and isolation of workloads
- separate node groups for system pods and app pods

## App deployment

- Deployment object manages desired state of podinfo app
- replicasets ensure spcified number of pod replicas are running
- pods pull the container image from ECR
- rolling restarts / updates for zero downtime deployments

## HPA

- HPA scales pods based on HTTP request rate
- metrics collected using Prometheus and exposed via cutom metrics API
- cluster autoscaler adjusts number of worker nodes based on pod resource requests

## Config and secrets

- configmaps store application config
- secrets store sensistive info
- both injected into pods as env var 
- AWS secrets manager integrated for managing secrets

## Monitoring/obs

- Prometheus collects metrics from the app and K8s componenets
- Grafana visualises metrics through dashboards
- Alertmanager sends alerts based on predefined rules
- Amazon Cloudwatch aggregates logs and provides additional metrics

## Security

- AW IRSA - allow pods to securely access AWS services without hardcoding credentials
- RBAC - restric actions within cluster
- network policies enforce ingress and egress rules at pod level
- image scanning to detect vulns
- Open Policy Agent (like Polaris) to enforce security standards

## Deployment

- CICD via Gitlab, automating testing, build and deploy via Flux. once MR approved and merged, Flux synchronises changes to cluster
- IaaC, such as Terraform, to provision AWS resources
- Helm values stored in git repos for version control

## Cost effectiveness

- autoscaling ensures resources scale up/down based on demand
- spot instances can be used for non-critical workloads to reduce costs
- resource quotas and limits prevent over-allocation
- regular reviews of cluster utilisation metrics so that right-sizing can be performed / automated

# Next steps

## Disaster recovery

- implement cross-region replication for cricial data
- set up regular backups and test recovery procedures

## Security

- implement a mesh, such as linkerd, for mutual TLS authentication between services

## Team training

- workshops familiarising team with unfamiliar tech
- documenting process, updating runbooks