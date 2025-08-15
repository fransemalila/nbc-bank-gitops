# NBC Bank GitOps Repository

A simple GitOps repository for deploying the NBC Bank application on Kubernetes using Flux.

## What This Is

This repository contains everything needed to run the NBC Bank application on a Kubernetes cluster. It uses GitOps principles, which means the cluster automatically stays in sync with what's defined here.

## What's Inside

```
nbc-bank-gitops/
├── clusters/my-cluster/           # Cluster configuration
│   ├── mysql-deployment.yaml      # MySQL database setup
│   ├── nbc-app-deployment.yaml   # The main bank application
│   ├── services.yaml              # Network services
│   ├── secrets.yaml               # Database passwords & keys
│   └── flux-system/               # Flux GitOps controller
└── templates/                      # Reusable templates
```

## What Gets Deployed

### 🗄️ MySQL Database
- A single MySQL 8.0 instance
- Stores all the bank data
- Runs on port 3306

### 🏦 NBC Bank App
- The main banking application
- Runs 3 copies for reliability
- Built from `fran123pv/nbc-bank-app:latest`
- Listens on port 8765

### 🔐 Secrets
- Database username/password
- JWT signing key for authentication

## Getting Started

### Prerequisites
- A Kubernetes cluster
- Flux installed (for GitOps)

### Quick Deploy
1. Clone this repository
2. Point Flux to this repo
3. Watch it deploy automatically!

## How It Works

1. **Flux watches this repository** for changes
2. **When you push changes**, Flux automatically updates the cluster
3. **The app stays running** with the latest configuration

## Making Changes

Want to update the app version? Just change the image tag in `nbc-app-deployment.yaml` and push. Flux will handle the rest!

## Need Help?

- Check if pods are running: `kubectl get pods -n nbc`
- View app logs: `kubectl logs -f deployment/nbc-app -n nbc`
- Check services: `kubectl get svc -n nbc`

## Security Note

⚠️ **Important**: The secrets in this repo are examples. In production, you should:
- Use proper secret management
- Rotate passwords regularly
- Never commit real secrets to git

---


