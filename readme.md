
# 🛠️ Internal Developer Platform (IDP) Home Lab

> **An end-to-end DevSecOps & Platform Engineering project designed for multi-team, multi-environment microservice delivery with GitOps, security gates, and observability built-in.**

## 📌 Overview

This home lab simulates a real-world **Internal Developer Platform** that enables developer teams to ship microservices securely and reliably via:

* 🔁 **GitOps-based Delivery** using Argo CD & Helm  
* 🧱 **Modular Terraform** for AWS infrastructure (multi-env)  
* 🔒 **Security-first CI/CD** with Trivy, SonarQube, OWASP ZAP  
* 📊 **Full-stack Observability** using Prometheus, Grafana & Loki  
* 🔐 **Secrets Management** with External Secrets + HashiCorp Vault  
* 🧑‍💻 **Developer Portal** using Backstage for self-service onboarding  

## 🧩 Architecture

```mermaid
graph TD
  Dev[Developer]
  GitHub[GitHub Repo]
  Actions[GitHub Actions]
  Argo[Argo CD]
  K8s[Kubernetes Cluster (EKS)]
  Terraform[Terraform Infra (VPC, EKS, RDS)]
  Vault[Vault + External Secrets]
  Prometheus[Prometheus + Grafana]
  ZAP[OWASP ZAP]
  Trivy[Trivy]
  Sonar[SonarQube]
  Backstage[Backstage Developer Portal]

  Dev --> GitHub
  GitHub --> Actions
  Actions --> Terraform
  Actions --> Argo
  Argo --> K8s
  Actions --> Trivy
  Actions --> ZAP
  Actions --> Sonar
  K8s --> Prometheus
  K8s --> Vault
  Dev --> Backstage
````

## ⚙️ Components

| Layer                    | Tools                                   | Description                                                    |
| ------------------------ | --------------------------------------- | -------------------------------------------------------------- |
| **IaC**                  | Terraform, AWS                          | Modular VPC, EKS, RDS, IAM roles with S3 state & DRY structure |
| **CI/CD**                | GitHub Actions, Helm                    | Secure pipeline with `validate → scan → test → deploy` flow    |
| **GitOps**               | Argo CD                                 | Automated Kubernetes sync, drift detection, Helm releases      |
| **Security**             | Trivy, ZAP, SonarQube, OPA              | SAST/SCA/DAST gates, branch protection, OPA CI policies        |
| **Secrets**              | External Secrets + Vault                | Dynamic secrets injection into pods                            |
| **Observability**        | Prometheus, Grafana, Loki, AlertManager | App metrics, logs, alerts, JVM dashboards                      |
| **Developer Experience** | Backstage                               | Onboarding docs, CI visibility, deploy buttons                 |

## 📁 Repository Structure

```
internal-dev-platform/
├── README.md
├── architecture/
│   └── platform-diagram.png
├── terraform/
│   ├── env/
│   │   ├── dev/
│   │   │   ├── main.tf
│   │   │   ├── variables.tf
│   │   │   └── dev.tfvars
│   └── modules/
│       ├── eks/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── vpc/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── ecr/
│       ├── rds/
│       └── vault/
├── kubernetes/
│   ├── base/
│   │   ├── namespaces/
│   │   ├── networkpolicies/
│   │   ├── rbac/
│   │   └── storageclass/
│   ├── apps/
│   │   └── team-a-app.yaml
│   ├── gitops/
│   │   └── argo-apps/
│   ├── security/
│   │   ├── trivy-scans/
│   │   ├── kyverno-policies/
│   │   └── falco-config/
│   ├── monitoring/
│   │   ├── prometheus/
│   │   ├── grafana/
│   │   └── alertmanager/
│   └── secrets/
│       └── external-secrets-config/
├── backstage/
│   ├── scaffolder-templates/
│   └── plugins/
├── cicd/
│   ├── github-actions/
│   └── gitlab-ci/
├── scripts/
│   ├── bootstrap.sh
│   └── destroy.sh
├── docs/
│   └── sops.md
└── Makefile

```

## 🚀 How to Run

1. **Provision Infra**

   ```bash
   cd terraform/envs/dev
   terraform init && terraform apply
   ```

2. **Install Argo CD**

   ```bash
   kubectl apply -n argocd -f kubernetes/base/argocd-install.yaml
   ```

3. **Bootstrap GitOps**

   ```bash
   argocd app create platform --repo=https://github.com/rahulambaragonda/idp-home-lab --path=kubernetes/base --dest-server=https://kubernetes.default.svc --dest-namespace=platform
   ```

4. **Trigger CI/CD**
   Push a change to `main`:

   * CI pipeline scans (Trivy, ZAP, SonarQube)
   * On pass, Argo CD auto-syncs to EKS

5. **Access Dashboards**

   * Grafana: `http://<lb-ip>:3000`
   * Argo CD: `http://<lb-ip>:8080`
   * Backstage: `http://<lb-ip>:7000`

## ✅ Key Features

* 🔐 **Zero Trust CI/CD** using GitHub Actions OIDC + IAM roles
* 🧪 **Promotion Gates**: only deploy if all scans pass
* 🧵 **Drift Detection** via Argo CD & Terraform validation
* 🧑‍🔬 **Self-Service Deployments** via Backstage plugins
* 📉 **Live Metrics + Logs** to debug issues in <5 mins

## 💡 Real-World Use Cases Simulated

* Multi-team microservice delivery (Web, Gaming, Payments)
* Environment-specific EKS clusters and namespaces
* Secure CI/CD lifecycle for regulated workloads (e.g., finance)
* Developer onboarding portal for faster velocity

## 🧠 Learnings

> This lab taught me how to:

* Treat infrastructure as a product for developers
* Embed security early in CI/CD (shift left)
* Apply GitOps at scale using ArgoCD & Helm
* Balance team autonomy with centralized platform control

## 🏁 Next Steps

* Integrate SLIs/SLOs with AlertManager and PagerDuty
* Add policy-as-code checks with OPA/Gatekeeper
* Create reusable GitHub Actions templates
* Enhance Backstage with plugins (Cost, Docs, Deploy)

---

## 📢 Let’s Connect

Built by [**Rahul Ambaragonda**](https://www.azuresimplified.com/)
💬 [LinkedIn](#) | 📝 [Hashnode](https://rahulambaragonda.hashnode.dev) | 📧 [Email](mailto:rahulambaragonda0@gmail.com)

---

Let me know if you want:

* A professional-looking project banner
* Diagrams as PNG/embedded instead of Mermaid
* A matching GitHub repo description + tags
* Help integrating OPA or Vault configs

This README is designed to make **recruiters say yes** — it sells your architecture, not just the tools.

```

If you want, I can also help you with:

- Creating a visually polished project banner  
- Generating PNG architecture diagrams  
- Drafting a compelling GitHub repo description  
- Adding sample Vault or OPA policies  

Would you like me to prepare any of these next?
```

