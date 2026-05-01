# Day 11 — Database, Outputs, Sidecars, Functions

## 🔹 Ansible — Install MariaDB
```
---
- name: Install MariaDB for Cloudops
  hosts: all
  become: yes

  tasks:
    - name: Install MariaDB
      yum:
        name: mariadb-server
        state: present

    - name: Start MariaDB
      service:
        name: mariadb
        state: started
        enabled: yes
```

## 🔹 Terraform — Output Public IP (r5.2xlarge)
```
resource "aws_instance" "CloudopsServer" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "r5.2xlarge"

  tags = {
    Name = "Cloudops-Output-EC2"
  }
}

output "CloudopsPublicIP" {
  value = aws_instance.CloudopsServer.public_ip
}
```

## 🔹 Kubernetes — Sidecar Container
```
apiVersion: v1
kind: Pod
metadata:
  name: Cloudops-sidecar
spec:
  containers:
    - name: main
      image: nginx
    - name: sidecar
      image: busybox
      command: ["sh", "-c", "echo Sidecar running; sleep 3600"]

```
## 🔹 Shell Script — Function Example
```
#!/bin/bash

greet() {
  echo "Welcome to Cloudops DevOps Training"
}

greet

```

# Notifications

## 🔹 Jenkins Pipeline — Email Notifications
You will learn how to **send email notifications on failure or success**.
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
    }
    post {
        success {
            mail to: 'team@Cloudops.com',
                 subject: "SUCCESS: Build #${env.BUILD_NUMBER}",
                 body: "Build completed successfully for $INSTITUTE_NAME"
        }
        failure {
            mail to: 'team@Cloudops.com',
                 subject: "FAILURE: Build #${env.BUILD_NUMBER}",
                 body: "Build failed for $INSTITUTE_NAME"
        }
    }
}
```
## 🔹 GitLab CI — Notifications
You will learn how to **notify via email after job completion**.
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
  after_script:
    - echo "Sending notification email to team@Cloudops.com"
```
