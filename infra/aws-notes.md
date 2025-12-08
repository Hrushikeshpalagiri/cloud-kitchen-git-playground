# AWS Notes

## Quick setup
- Install AWS CLI: `curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"` (macOS) or use package manager.
- Configure: `aws configure` (access key, secret, region, output).
- Use named profiles: `aws configure --profile myprofile`.

## Identity & Access Management (IAM)
- Use least-privilege policies.
- Prefer IAM roles for EC2 / Lambda / ECS instead of long-lived keys.
- Use IAM groups for shared permissions and SCPs for Organizations.
- Enable MFA for privileged users.

## Networking (VPC)
- Use multiple AZs for high availability.
- Subnet layout: public (bastion/NAT gateway), private (app), DB (private isolated).
- Use Security Groups (stateful) and NACLs (stateless) appropriately.
- Route tables: separate per subnet type; use IGW for public, NAT for outbound from private.

## Compute
- EC2: choose instance family by workload (t3/t4g for general purpose, c/m/r for cpu/memory).
- Use Auto Scaling Groups + Launch Templates for scaling.
- Use AMIs, SSM Session Manager for access without SSH keys.
- Lambda: keep functions small, reuse layers for dependencies, monitor cold starts.

## Storage
- S3: versioning, lifecycle rules, MFA delete (where needed), block public access by default.
- EBS: use gp3 for most workloads; snapshot lifecycle for backups.
- EFS: shared POSIX for multiple instances; choose throughput/performance mode.
- Encryption: enable KMS-managed keys (SSE-KMS) for sensitive data.

## Databases
- RDS: Multi-AZ for HA; read replicas for read scale; automated backups and snapshots.
- Aurora: good for high-performance MySQL/Postgres compatible workloads.
- DynamoDB: serverless, provisioned or on-demand capacity, use GSIs/LSIs carefully.
- Redis / Elasticache: use clusters for replication and failover.

## Kubernetes (EKS)
- Use managed node groups or Fargate for serverless pods.
- Use cluster autoscaler, IAM Roles for Service Accounts (IRSA).
- Keep control plane logs enabled and enable cluster logging to CloudWatch.

## Infrastructure as Code
- Terraform or CloudFormation/CDK: keep state in remote backend (S3 + DynamoDB locking for Terraform).
- Use modular, reusable modules and environment-specific variables.
- Review drift and run plan/preview before apply.

## Observability & Monitoring
- CloudWatch: metrics, logs, alarms; use CloudWatch Agent for host metrics.
- X-Ray for distributed tracing.
- Centralized logging (CloudWatch Logs or ELK/OpenSearch).
- Define SLOs and alerting thresholds.

## Security & Compliance
- Enable AWS Config and Security Hub for posture monitoring.
- Use GuardDuty for threat detection.
- Rotate credentials, enforce password policies.
- Audit with CloudTrail (log all regions, deliver to centralized S3).

## Cost management
- Use Cost Explorer and Budgets.
- Tag resources for chargeback and visibility.
- Prefer Savings Plans / Reserved Instances for steady-state workloads.
- Clean up unused resources (volumes, snapshots, unattached ENIs).

## Useful CLI snippets
- List instances: `aws ec2 describe-instances --query 'Reservations[].Instances[].{ID:InstanceId,State:State.Name,Type:InstanceType,AZ:Placement.AvailabilityZone}'`
- S3 sync: `aws s3 sync ./site s3://my-bucket --delete`
- Get current region: `aws configure get region --profile myprofile`

## Best practices
- Use multi-account strategy (Landing Zone/Organizations) for isolation.
- Automate security checks and CI/CD deployments.
- Document runbooks and incident response playbooks.
- Test backups and DR plans regularly.

References:
- Official AWS docs and Well-Architected Framework for detailed guidance.
- Keep IAM keys out of repo; use secrets manager / parameter store.
