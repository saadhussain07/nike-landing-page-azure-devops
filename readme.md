# Nike Landing Page — Azure DevOps CI/CD Deployment

> A production-style CI/CD pipeline built on Microsoft Azure, deploying a React + Vite + Tailwind CSS Nike landing page to Azure App Service via Azure Artifacts and Release Pipelines.

**Author:** Muhammad Saad Hussain | [@saadhussain07](https://github.com/saadhussain07)  
**LinkedIn:** [muhammad-saad-hussain](https://www.linkedin.com/in/muhammad-saad-hussain-28435b3a2/)  

---

## Pipeline Status

![Pipeline](https://img.shields.io/badge/Azure%20Pipeline-Passing-brightgreen?style=flat&logo=azuredevops)
![Platform](https://img.shields.io/badge/Azure%20App%20Service-South%20India-0078D4?style=flat&logo=microsoftazure)
![Stack](https://img.shields.io/badge/React%20%2B%20Vite%20%2B%20Tailwind-deployed-38BDF8?style=flat)

---

## Pipeline Flow

```
git push → Azure Repos → Azure Pipelines → Azure Artifacts → Release Pipeline → Azure App Service
```

Every push to `main` triggers the full automated pipeline — install, build, publish artifact, deploy. Zero manual steps.

---

## Project Structure

```
nike-landing-page-azure-devops/
├── public/                    # Static assets
├── src/                       # React source code
├── .eslintrc.cjs              # ESLint configuration
├── .gitignore
├── index.html                 # Vite entry point
├── package.json               # Dependencies & scripts
├── package-lock.json
├── postcss.config.js          # PostCSS config for Tailwind
├── tailwind.config.js         # Tailwind CSS configuration
├── vite.config.js             # Vite build configuration
└── azure-pipelines.yml        # Azure DevOps CI pipeline
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React + Vite |
| Styling | Tailwind CSS + PostCSS + Autoprefixer |
| Source Control | Azure Repos |
| CI/CD | Azure Pipelines (YAML) |
| Package Registry | Azure Artifacts (npm feed) |
| Build Agent | Ubuntu Latest (Microsoft-hosted) |
| Hosting | Azure App Service — South India |
| Release | Azure Release Pipelines |

---

## Build Pipeline

Defined in `azure-pipelines.yml` — triggers on every push to `main`:

```yaml
trigger:
- main

stages:
- stage: Build
  jobs:
  - job: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'install -D tailwindcss postcss autoprefixer'

    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build'

    - task: Npm@1
      inputs:
        command: 'publish'
        workingDir: './dist'
        publishRegistry: 'useFeed'
        publishFeed: '$FEED_DETAILS'
```

**Pipeline stages:**
1. Agent spins up on `ubuntu-latest`
2. Tailwind CSS, PostCSS, Autoprefixer installed
3. `npm run build` compiles production bundle via Vite
4. `./dist` output published to Azure Artifacts npm feed

---

## Release Pipeline

After artifact is published, Release Pipeline deploys to App Service:

```bash
# Post-deployment inline script
cp -rf /home/site/wwwroot/package/* /home/site/wwwroot/
```

**Required App Service settings** — add these in Configuration → Application Settings:

| Setting | Value |
|---------|-------|
| `WEBSITE_DYNAMIC_CACHE` | `0` |
| `WEBSITE_LOCAL_CACHE_OPTION` | `Never` |

These disable file caching so every deployment reflects instantly.

---

## Pipeline Stats

| Metric | Value |
|--------|-------|
| Build time | ~26 seconds |
| Agent | ubuntu-latest |
| Region | South India |
| Status | ✅ Passing |

---

## Local Setup

```bash
# Clone the repo
git clone https://github.com/saadhussain07/nike-landing-page-azure-devops
cd nike-landing-page-azure-devops

# Install dependencies
npm install

# Run dev server
npm run dev

# Build for production
npm run build
```

---

## How to Replicate the Azure Pipeline

1. Import this repo into **Azure Repos**

2. Create a new **Azure Pipelines** pipeline from `azure-pipelines.yml`

3. Create an **Azure Artifacts** npm feed and update `$FEED_DETAILS` with your feed ID

4. Create a **Release Pipeline** linked to the artifact feed, targeting your App Service

5. Add App Service settings:
   - `WEBSITE_DYNAMIC_CACHE` = `0`
   - `WEBSITE_LOCAL_CACHE_OPTION` = `Never`

6. Push to `main` — pipeline runs automatically ✅

---

## Related Projects

- [🦊 GitLab CI/CD Projects](https://github.com/saadhussain07/gitlab-cicd-projects) — Self-hosted GitLab pipelines
- [⚙️ Jenkins Pipeline](https://github.com/saadhussain07/jenkins-pipeline) — Declarative Jenkins CI/CD
- [📊 K8s Monitoring Stack](https://github.com/saadhussain07/k8s-monitoring-stack) — Prometheus + Grafana + Loki
- [🏗️ Terraform AWS Infra](https://github.com/saadhussain07/terraform-aws-infra) — Modular AWS infrastructure
- [🌐 RocketDevOps](https://rocketdevops.vercel.app) — DevOps learning platform

---

<p align="center">⭐ Star this repo if it helped you!</p>
