🟦 1. What is your day-to-day activity?

My day-to-day responsibilities include:

Monitoring CI/CD pipelines, application deployments, Kubernetes resources, cluster health, and logs.

Working on deployment-related tasks, writing YAML files, Dockerfiles, Helm charts, and fixing pipeline failures.

Troubleshooting production & lower-environment issues (pods failing, image pull errors, secrets/config issues).

Managing access, secrets, and identities using Azure Key Vault and Managed Identities.

Automating workflows using Bash, PowerShell, or Python scripts.

Improving infrastructure using IaC tools such as Terraform/Bicep.

Participating in daily stand-up meetings and coordinating with dev/testing teams.

🟦 2. If a file is used by two customers and needs deployment in Kubernetes & On-Prem, how do you handle it?

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

🟦 3. What is the issue with using large images in Dockerfile?

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

🟦 4. How to deploy an app to Kubernetes cluster (CD Pipeline)?

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

🟦 5. If secret is stored in Key Vault and pod goes down, how do you troubleshoot?

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

🟦 6. How Azure Key Vault integrates with CI/CD?
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

🟦 7. What to do if an application is down?
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

🟦 8. Scenario-Based Questions
✔ a) Customer asks for update after service down for 2 weeks — what do you say?

“We apologize for the inconvenience. The issue is already under high priority.
Our team is actively working on the root cause and resolution.
We will share an updated timeline and RCA shortly.”

✔ b) How do you troubleshoot?

Checklist:

Pod logs

Deployment/replica issues

Node status

Image issues

ConfigMaps & Secrets

Network policies

Ingress/Service issues

APM logs (Datadog/AppInsights)

✔ c) How to avoid this in future?

Add monitoring & alerting

Add liveness & readiness probes

Improve CI/CD testing

Implement canary/blue-green deployments

Resource scaling (HPA)

Use logging and distributed tracing

🟦 9. Dockerfile Example
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000

CMD ["npm", "start"]

🟦 10. Deployment YAML Example
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
