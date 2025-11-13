🟦 **1. What is your day-to-day activity?**

My day-to-day responsibilities include:

Monitoring CI/CD pipelines, application deployments, Kubernetes resources, cluster health, and logs.

Working on deployment-related tasks, writing YAML files, Dockerfiles, Helm charts, and fixing pipeline failures.

Troubleshooting production & lower-environment issues (pods failing, image pull errors, secrets/config issues).

Managing access, secrets, and identities using Azure Key Vault and Managed Identities.

Automating workflows using Bash, PowerShell, or Python scripts.

Improving infrastructure using IaC tools such as Terraform/Bicep.

Participating in daily stand-up meetings and coordinating with dev/testing teams.

🟦 **2. If a file is used by two customers and needs deployment in Kubernetes & On-Prem, how do you handle it?**

Approach:

✔ Maintain a single source of truth

Store the file in a Git repository.

✔ For Kubernetes

Create a ConfigMap or Secret (depending on sensitivity).

Mount it in the pod as a volume.

Deploy using:

kubectl apply -f <file>

or Helm chart values override

✔ For On-Prem

Use CI/CD pipeline task to copy file:

scp file.txt user@onprem:/path


Or use Ansible/Powershell to deploy the file.

Result: Same file → deployed to both environments consistently.

🟦** 3. What is the issue with using large images in Dockerfile?**

Problems:

Slow image build time

Slow push/pull from registry

Larger storage usage

Delayed Kubernetes pod startup

Higher security vulnerability surface

💡 Solution:

Use minimal base images (alpine, slim)

Use multi-stage builds

Copy only required files

Clear cache & temp files

🟦 **4. How to deploy an app to Kubernetes cluster (CD Pipeline)?**

Deployment Flow:

Developer pushes code → Git

CI Pipeline:

Install dependencies

Run tests

Build Docker image

Push to registry (ACR/ECR/GCR)

CD Pipeline:

Update Deployment YAML / Helm values

Run:

kubectl apply -f deployment.yaml


or

helm upgrade --install app chart/


Validate deployment:

kubectl get pods
kubectl logs

🟦 **5. If secret is stored in Key Vault and pod goes down, how do you troubleshoot?**

Steps:

Check pod error:

kubectl describe pod <pod>


Check logs:

kubectl logs <pod>


Verify Key Vault access:

Managed Identity permission

Access policy / RBAC

Pod Identity (AAD Pod Identity / Workload Identity)

Network:

Firewall blocked

Private Endpoint issue

DNS issues

Check secret exists:

az keyvault secret show --name <secret-name>

🟦 **6. How Azure Key Vault integrates with CI/CD?**
✔ Azure DevOps Pipeline
- task: AzureKeyVault@2
  inputs:
    azureSubscription: 'service-connection'
    keyVaultName: 'myvault'
    secretsFilter: '*'

✔ Kubernetes Integration

CSI Secrets Store Driver

Managed Identity

Pod Identity

Secrets are mounted directly into pods.

🟦 **7. What to do if an application is down?**
Troubleshooting Steps:
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>


Common issues:

CrashLoopBackOff

ImagePullBackOff

ConfigMap/Secret mount failure

Node NotReady

Liveness/Readiness probe failures

Resource limits exceeded (OOMKilled)

🟦 **8. Scenario-Based Questions**
✔ **a) Customer asks for update after service down for 2 weeks — what do you say?**

“We apologize for the inconvenience. The issue is already under high priority.
Our team is actively working on the root cause and resolution.
We will share an updated timeline and RCA shortly.”

✔** b) How do you troubleshoot?**

Checklist:

Pod logs

Deployment/replica issues

Node status

Image issues

ConfigMaps & Secrets

Network policies

Ingress/Service issues

APM logs (Datadog/AppInsights)

**✔ c) How to avoid this in future?**

Add monitoring & alerting

Add liveness & readiness probes

Improve CI/CD testing

Implement canary/blue-green deployments

Resource scaling (HPA)

Use logging and distributed tracing

🟦** 9. Dockerfile Example**
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000

CMD ["npm", "start"]

🟦 **10. Deployment YAML Example**
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myrepo/myapp:v1
        ports:
        - containerPort: 3000

        ============================================================================


  ✅** 1. In Git, explain the push and pull commands.**
git push

Sends your local commits → to the remote repository (GitHub, GitLab, Bitbucket).

Updates the remote branch with your changes.

Example:

git push origin main

git pull

Fetches changes from the remote repository → merges them into your local branch.

Equivalent to:

git fetch + git merge


**Example:**
-------
git pull origin main
--------
✅ **2. What is the use of Git tags?**

Git tags are used to:

Mark important points in history (mostly releases)

Identify versions (v1.0, v2.1, v3.5.1)

Example:

git tag v1.0
git push origin v1.0


Tags are commonly used for:

Release management

Versioning

CI/CD deployments

✅** 3. What are the different types of branches in Git?**

Common branching structure:

✔ Main/Master Branch

Production-ready code

Always stable

✔ Develop Branch

Integrated development

Next release preparation

✔ Feature Branch

Used for new features

Merged after review

✔ Bugfix Branch

Used to fix defects

✔ Hotfix Branch

Urgent critical fixes directly on production

✔ Release Branch

Pre-production testing and staging

✅** 4. How do you write an Ansible playbook, and what client requirements do you consider?**
Basic Playbook Example
- name: Install Apache
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache package
      apt:
        name: apache2
        state: present

Client requirements considered:

OS type (Ubuntu, CentOS, Amazon Linux)

User permissions (sudo privileges)

Network connectivity

Package manager (apt, yum)

Application version requirement

Environment (dev/test/prod)

Security constraints (SSH keys, vault encryption)

✅** 5. In Python, what are lists and tuples, and how do they differ?**
✔ List

Mutable (can change)

Defined using []

Example:

my_list = [1, 2, 3]
my_list[0] = 10  # allowed

✔ Tuple

Immutable (cannot change)

Defined using ()

Example:

my_tuple = (1, 2, 3)
# my_tuple[0] = 10 → Not allowed

Key Differences
Feature	List	Tuple
Mutability	Mutable	Immutable
Syntax	[]	()
Speed	Slower	Faster
Use-case	Dynamic data	Fixed data
✅ **6. In CloudWatch, what is the use of log groups and log trails?**
✔ Log Groups

A collection of log streams.

Example: /aws/lambda/app1, /ecs/service1.

Organizes logs by application or service.

✔ Log Streams

Sequence of log events from a specific instance/container.

✔ Log Trails (CloudTrail)

Records API actions done in your AWS account.

Tracks who did what and when (audit).

Usage:

Security auditing

Compliance

Investigating unauthorized activity

✅ **7. In Terraform, what is the purpose of init, plan, and apply commands?**
terraform init

Initializes working directory

Downloads providers & modules

terraform plan

Shows what Terraform will create/update/destroy

No changes applied yet

terraform apply

Applies the changes

Provisions real infrastructure

✅** 8. What happens if the Terraform state file is accidentally deleted?**

State file (terraform.tfstate) contains actual resource mappings.

If deleted:

✔ Issues:

Terraform loses track of resources

Cannot detect changes

Might try to recreate all resources → downtime risk

✔ Solutions:

Restore from backup (best

S3 remote backend

Terraform Cloud

Azure Storage

Import resources manually

terraform import aws_s3_bucket.mybucket mybucketname

✅ **9. What is the purpose of creating S3 bucket policies?**

Bucket policies are used to control access to the bucket.

Purpose:

Allow/deny read/write access

Restrict access to specific IAM users/roles

Enforce encryption

Enforce IP-based restrictions

Allow cross-account access

Example:

{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*"
}

✅ **10. How do you maintain the lifecycle of an S3 bucket?**

Using Lifecycle Rules:

Transition objects to cheaper storage (STANDARD → IA → Glacier)

Delete old objects automatically

Delete incomplete multipart uploads

Manage versions with expiration rules

Example:

After 30 days → move to IA

After 90 days → move to Glacier

After 365 days → delete

✅ **11. In Airflow, if a job fails, how do you debug it?**
Steps:

Check task logs in UI

Check retry count and failure reason

Validate DAG code (syntax errors)

Check upstream/downstream dependencies

Verify environment variables and connections

Check worker or scheduler logs

Check external system:

Database

API

Storage

Common fixes:

Increase timeout

Fix Python exception

Fix missing credentials

Restart scheduler

Enable retries

✅** 12. If you’re facing performance issues on a server, how do you troubleshoot?**
Step-by-step diagnosis:
✔ CPU Issues:
top
htop

✔ Memory Issues:
free -m
dmesg | grep OOM

✔ Disk Issues:
df -h
iostat

✔ Network Issues:
ping
traceroute
netstat -tulpn

✔ Logs:
/var/log/syslog
/var/log/messages

✔ App-level checks:

Slow code

Database bottleneck

Unoptimized queries

✔ Fixes:

Restart service

Increase server size

Add load balancer

Enable caching

Optimize DB      
