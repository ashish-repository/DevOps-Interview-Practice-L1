# Day 09 — Templates, Internet Gateway, ConfigMap, CPU Load

## 🔹 Ansible — Use Jinja2 Template
```
---
- name: Deploy template for Cloudops
  hosts: all
  become: yes

  tasks:
    - name: Copy template file
      template:
        src: Cloudops.conf.j2
        dest: /etc/Cloudops.conf
```

## 🔹 Terraform — Internet Gateway
```
resource "aws_internet_gateway" "CloudopsIGW" {
  vpc_id = aws_vpc.CloudopsVPC.id

  tags = {
    Name = "Cloudops-IGW"
  }
}
```

## 🔹 Kubernetes — Create ConfigMap
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: Cloudops-config
data:
  APP_MODE: "production"
  WELCOME_MSG: "Hello Cloudops"
```

## 🔹 Shell Script — Check CPU Load
```
#!/bin/bash

LOAD=$(uptime | awk '{print $10}')

echo "Current CPU Load: $LOAD"
```

# Artifacts and Archiving

## 🔹 Jenkins Pipeline — Archive Artifacts
You will learn how to **save build artifacts for future use**.
```
pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "Cloudops"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ashishcloudops01/sample-maven-project.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}
```
## 🔹 GitLab CI — Artifacts
You will learn how to **store artifacts in GitLab CI**.
```
stages:
  - build

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - git clone https://github.com/ashishcloudops01/sample-maven-project.git
    - cd sample-java-app
    - mvn clean package
  artifacts:
    paths:
      - target/*.jar
    expire_in: 1 week
```
