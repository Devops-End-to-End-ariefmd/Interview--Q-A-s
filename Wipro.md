DevOps & Kubernetes Interview Preparation – Wipro

This document contains real-time DevOps interview questions and answers based on Kubernetes, Docker, CI/CD, Azure DevOps, Key Vault, troubleshooting, and scenario-based questions.

🟦 1. What is your day-to-day activity?

My day-to-day responsibilities include:

Monitoring CI/CD pipelines, application deployments, Kubernetes resources, cluster health, and logs.

Working on deployment activities, writing YAML files, Dockerfiles, Helm charts, and fixing pipeline failures.

Troubleshooting production and lower-environment issues (pods failing, image pull errors, secrets/config issues).

Managing access, secrets, and identities using Azure Key Vault and Managed Identities.

Automating workflows with scripts (Bash, PowerShell, Python).

Improving infrastructure using IaC such as Terraform/Bicep.

Participating in daily stand-ups and coordinating with development and testing teams.

🟦 2. If a file is used by two customers and needs deployment in Kubernetes and On-Prem, how to do it?

Approach:

✔ Maintain a single source of truth

Store the file in a Git repository.

✔ For Kubernetes

Create a ConfigMap or Secret depending on the file type.

Mount it inside the pod using a volume.

Deploy using:

kubectl apply

or Helm chart

✔ For On-Prem

Use CI/CD pipeline to copy the file using:

SCP/SSH

Ansible

PowerShell Remoting

Deploy the same version of the file to the on-prem server.

Benefit:
One file → version-controlled → deployed to multiple environments consistently.

🟦 3. What is the issue with using a large image in a Dockerfile?

Problems with large Docker images:

Slow build time

Slow push/pull from registry

High storage usage in registry

Slower pod startup time → increased deployment time

More security vulnerabilities (large base image)

Cache invalidation happens more often

✔ Solution:

Use alpine or minimal base image

Use multi-stage builds

Remove temporary files

Copy only required files

🟦 4. How do you deploy an app to Kubernetes? (CD part only)

CI/CD deployment flow:

Developer commits code → Git repo

CI builds the app:

Run tests

Build Docker image

Push to ACR/ECR/GCR

CD deploys to Kubernetes:

Fetch latest image tag

Update deployment manifest/Helm values

Apply changes using:

kubectl apply

helm upgrade --install

or GitOps tools like ArgoCD

Post-deployment steps:

Check pod status

Validate logs

Verify service/ingress

🟦 5. If secret is stored in vault and pod is down, how to troubleshoot?

Steps to troubleshoot:

Check pod errors

kubectl describe pod <pod-name>


Check logs

kubectl logs <pod>


Check vault access

Managed Identity not assigned

Wrong policies assigned in Key Vault

Pod Identity not configured

Check networking issues

VNet / Firewall blocking

DNS resolution failure

Private endpoint misconfiguration

Check if vault secret exists

az keyvault secret show --name <secret>

🟦 6. How is Azure Key Vault integrated in CI/CD?
✔ In Azure DevOps pipeline:

Use KeyVault task:

- task: AzureKeyVault@2
  inputs:
    azureSubscription: 'service-connection'
    keyVaultName: 'my-kv'
    secretsFilter: '*'


Pipeline uses secrets as environment variables.

✔ In Kubernetes:

Use:

CSI Secrets Store Driver

AAD Pod Identity / Workload Identity

Managed Identity

Secrets are injected into Kubernetes pods securely.

🟦 7. What to do if an application is down?

Steps:

Check pod status:

kubectl get pods


Describe pod:

kubectl describe pod <name>


Check logs:

kubectl logs <pod>


Common issues:

ImagePullBackOff

CrashLoopBackOff

ConfigMap/Secret not mounted

Liveness/Readiness probe failure

Resource limits exceeded (OOMKilled)

Node NotReady

Check service/ingress connectivity.

🟦 8. Scenario-based Questions
a) If service is down for more than 2 weeks and customer asks for an update — how would you respond?

Professional response:

“We understand the impact and apologize for the inconvenience. The issue is already under high priority. Our engineering team is actively working on the root cause. We will share the RCA and the expected resolution timeline. Meanwhile, we are providing temporary workarounds to minimize business impact.”

b) How do you troubleshoot the issue and what do you check?

Checklist:

Logs (app/db)

Pod events

Node health

Deployment changes

Registry image availability

ConfigMaps & Secrets

Resource usage

Network policies

Ingress/service issues

c) What steps to take so the issue does not happen again?

Preventive steps:

Add monitoring & alerts (Prometheus + Grafana)

Add liveness/readiness probes

Use autoscaling (HPA)

Implement canary/blue-green deployments

Improve testing (unit, regression, load tests)

Enable centralized logging (ELK/EFK)

Add retry & fallback mechanisms

🟦 9. Sample Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]

🟦 10. Sample Deployment YAML
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
