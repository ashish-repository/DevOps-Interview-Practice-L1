# Day 07 — SPEED TYPING

Rewrite ALL FOUR from scratch:

1. Ansible: Install nginx
2. Terraform: EC2 (choose any Cloudops instance)
3. Kubernetes: Deployment 2 replicas
4. Shell: Print date + uptime

Set a timer: **20 minutes**


# Parameterized Builds

## 🔹 Jenkins Pipeline — Parameterized
You will learn how to **create parameterized builds in Jenkins**.
```
pipeline {
    agent any
    parameters {
        string(name: 'ENVIRONMENT', defaultValue: 'dev', description: 'Deployment Environment')
    }
    environment {
        INSTITUTE_NAME = "Cloudops"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ashishcloudops01/sample-maven-project.git'
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo "Deploying to $ENVIRONMENT by $INSTITUTE_NAME"'
            }
        }
    }
}
```
## 🔹 GitLab CI — Parameterized via Variables
You will learn how to **use variables to parameterize GitLab jobs**.
```
stages:
  - deploy

variables:
  INSTITUTE_NAME: "Cloudops"
  ENVIRONMENT: "dev"

deploy:
  stage: deploy
  image: alpine:latest
  script:
    - echo "Deploying to $ENVIRONMENT by $INSTITUTE_NAME"
```
