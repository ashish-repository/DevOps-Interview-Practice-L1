# Day 23 — Tags, EIP, HPA, Kill Process

## 🔹 Ansible — Add Tags to Users
```
---
- name: Create users with comments
  hosts: all
  become: yes

  tasks:
    - name: Create Cloudops user
      user:
        name: devuser
        comment: "Cloudops Developer User"
```

## 🔹 Terraform — Elastic IP
```
resource "aws_eip" "CloudopsEIP" {
  instance = aws_instance.CloudopsEC2.id

  tags = {
    Name = "Cloudops-EIP"
  }
}

```
## 🔹 Kubernetes — Horizontal Pod Autoscaler (HPA)
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: Cloudops-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: Cloudops-deploy
  minReplicas: 2
  maxReplicas: 6
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50

```
## 🔹 Shell Script — Kill High CPU Process
```
#!/bin/bash

PID=$(ps aux --sort=-%cpu | awk 'NR==2{print $2}')

echo "Killing process: $PID"
kill -9 $PID
```
# Docker Integration

## 🔹 Jenkins Pipeline — Build Docker Image
You will learn how to **build and tag Docker images** with proper labels.
```
pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "Cloudops"
        TEAM = "DevOps"
        ENV = "staging"
        PROJECT = "WebApp"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ashishcloudops01/sample-maven-project.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t Cloudops/webapp:${ENV}-${BUILD_NUMBER} \
                --label "INSTITUTE_NAME=$INSTITUTE_NAME" \
                --label "TEAM=$TEAM" \
                --label "ENV=$ENV" \
                --label "PROJECT=$PROJECT" .
                '''
            }
        }
    }
}
```
## 🔹 GitLab CI — Build Docker Image
You will learn how to **build Docker images in GitLab CI**.
```
stages:
  - docker-build

variables:
  INSTITUTE_NAME: "Cloudops"
  TEAM: "DevOps"
  ENV: "staging"
  PROJECT: "WebApp"

docker-build:
  stage: docker-build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t Cloudops/webapp:${ENV}-${CI_PIPELINE_ID} \
      --label "INSTITUTE_NAME=$INSTITUTE_NAME" \
      --label "TEAM=$TEAM" \
      --label "ENV=$ENV" \
      --label "PROJECT=$PROJECT" .
```
