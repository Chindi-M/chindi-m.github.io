---
layout: post
title: "Understanding GitHub Actions and Operational Workflows"
date: 2025-01-03
categories: [devops, automation]
tags: [github-actions, ci-cd, workflow, infrastructure-as-code, yaml, automation, devops, tutorial, beginner-friendly, hands-on]
---


GitHub Actions provides automation for building, testing and deploying code. It also supports operational tasks such as scanning infrastructure, checking security, updating dependencies and coordinating deployments. This guide introduces the structure, vocabulary and typical workflows used by DevOps engineers. It focuses on practical steps so that you can follow along in your own repository.

---

## 1. The Layout of GitHub Actions

GitHub Actions uses a simple folder and file structure. Everything is placed inside the repository.

```
repo/
  .github/
    workflows/
      example-workflow.yml
```

Any YAML file inside the workflows folder becomes an automated workflow. GitHub reads the triggers defined inside the file and runs it at the correct time.

---

## 2. Anatomy of a Workflow File

A workflow contains:

- The name of the workflow
- The events that trigger it
- One or more jobs
- Steps inside each job
- Actions that run inside the steps

Here is a simple example:

```yaml
name: Build and Test

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4
      
      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version: "18"
      
      - name: Install dependencies
        run: npm install
      
      - name: Run tests
        run: npm test
```

This workflow runs whenever someone pushes to the main branch or opens a pull request. It checks out the code, installs Node, installs dependencies and runs tests.

---

## 3. Common Trigger Types

Workflows can run in many situations. These are the ones DevOps teams use most frequently.

**On push:**

```yaml
on:
  push:
    branches:
      - main
```

**On pull request:**

```yaml
on: pull_request
```

**On a schedule:**

```yaml
on:
  schedule:
    - cron: "0 3 * * *"
```

This example runs every day at 03:00.

**On manual dispatch:**

```yaml
on: workflow_dispatch
```

This adds a button in the GitHub interface, which is useful for operational tasks.

---

## 4. Jobs and Runners

A workflow contains jobs. Each job runs on a runner. A runner is a virtual machine that GitHub provides. You can use Ubuntu, Windows or macOS.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

You can define multiple jobs if you want to run tasks in parallel or in a sequence:

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
  
  test:
    runs-on: ubuntu-latest
    needs: lint
```

The `needs` keyword forces one job to wait for another.

---

## 5. Steps and Actions

Steps run sequentially within a job. There are two types:

### 1. Actions

Actions are reusable components written by GitHub or community contributors.

```yaml
- name: Check out code
  uses: actions/checkout@v4
```

### 2. Raw shell commands

```yaml
- name: Build
  run: npm run build
```

---

## 6. Storing Secrets and Variables

Many workflows need sensitive information such as API keys or credentials. These can be stored safely as secrets.

1. Open your repository
2. Go to **Settings**
3. Select **Secrets and variables**
4. Add your secrets and variables

You can reference a secret inside a workflow as:

```yaml
env:
  API_KEY: ${{ secrets.API_KEY }}
```

**Never store secrets directly inside code or in YAML files.**

---

## 7. Typical DevOps Workflows

Below are common patterns used in real DevOps environments.

### A. Continuous Integration Workflow

This workflow checks out code, installs dependencies and runs tests every time there is a push or pull request.

```yaml
name: CI

on:
  push:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test
```

This gives early and consistent feedback.

### B. Continuous Delivery Workflow

This workflow builds an application and publishes an artefact for deployment.

```yaml
name: Build Artefact

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install
      - run: npm run build
      
      - name: Upload build artefact
        uses: actions/upload-artifact@v4
        with:
          name: app-build
          path: ./dist
```

The artefact can be used by another workflow to deploy infrastructure or applications.

### C. Infrastructure as Code Workflow

Many DevOps teams store Terraform, Ansible or CloudFormation code in GitHub. You can create a workflow that validates your infrastructure changes.

Here is a simple Terraform plan workflow:

```yaml
name: Terraform Plan

on:
  pull_request:

jobs:
  plan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v3
      
      - name: Terraform init
        run: terraform init
      
      - name: Terraform plan
        run: terraform plan -no-color
```

This allows reviewers to see what will change before applying it.

### D. Deployment Workflow

This example deploys to a cloud platform. It runs when you tag a commit as a release.

```yaml
name: Deploy

on:
  push:
    tags:
      - "v*.*.*"

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Log in to cloud
        run: |
          cloud login --token ${{ secrets.CLOUD_TOKEN }}
      
      - name: Deploy application
        run: ./deploy.sh
```

Deployment scripts are often kept separate for clarity.

---

## 8. Operational Workflows

Operational workflows perform tasks that are not directly related to application releases, although they might support them.

### A. Dependency updates

GitHub provides Dependabot, which can open pull requests automatically.

### B. Security scanning

You can run security scans daily:

```yaml
name: Security Scan

on:
  schedule:
    - cron: "0 4 * * *"

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: github/codeql-action/init@v3
      - uses: github/codeql-action/analyse@v3
```

### C. Database backups

```yaml
name: Daily Backup

on:
  schedule:
    - cron: "0 1 * * *"

jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - run: ./backup-db.sh
```

### D. Infrastructure housekeeping

This can include:

- Tagging resources
- Cleaning up old artefacts
- Running cost or compliance checks

These workflows often run on a schedule.

---

## 9. Pull Request Workflows

It is common practice for the workflow to run tests and security checks automatically when a pull request is opened. This helps reviewers understand whether the proposed changes are safe to merge.

```yaml
on: pull_request
```

The workflow may leave a comment or a status check that states if the code is safe to merge.

---

## 10. Tips for Managing Workflows in a Team

- Use clear workflow names so that your teammates understand their purpose
- Keep sensitive information out of the workflow file. Use secrets and variables
- Keep your workflows small and focused
- Store shared script logic in separate files
- Add comments in the YAML file to explain why something exists
- Test your workflows in a feature branch before merging

---

## Key Takeaway

GitHub Actions is a core tool in the DevOps toolkit. It automates builds, tests, deployments and operational routines. When you understand triggers, jobs, steps and secrets, you can build workflows for nearly any task your team performs.


---

