# Day 15 — Java, S3 Bucket, CronJob, File Reader

## 🔹 Ansible — Install Java & Set JAVA_HOME
```
---
- name: Install Java on Cloudops server
  hosts: all
  become: yes

  tasks:
    - name: Install OpenJDK
      yum:
        name: java-11-openjdk
        state: present

    - name: Set JAVA_HOME
      lineinfile:
        path: /etc/profile
        line: 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk'
```

## 🔹 Terraform — Create S3 Bucket
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "CloudopsBucket" {
  bucket = "Cloudops-devops-bucket"

  tags = {
    Name = "CloudopsBucket"
  }
}
```

## 🔹 Kubernetes — CronJob
```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: Cloudops-cron
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: job
              image: busybox
              command: ["sh", "-c", "echo Cloudops CronJob running"]
          restartPolicy: OnFailure
```

## 🔹 Shell Script — Read File Line-by-Line
```
#!/bin/bash

FILE="/etc/passwd"

while read line; do
  echo "Line: $line"
done < $FILE
```

# Advanced Notifications

## 🔹 Jenkins Pipeline — Slack Notification
You will learn how to **send Slack notifications after build success or failure**.
```
pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "Cloudops"
        SLACK_CHANNEL = "#devops"
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
            slackSend(channel: "$SLACK_CHANNEL", message: "SUCCESS: $INSTITUTE_NAME Build #${env.BUILD_NUMBER}")
        }
        failure {
            slackSend(channel: "$SLACK_CHANNEL", message: "FAILURE: $INSTITUTE_NAME Build #${env.BUILD_NUMBER}")
        }
    }
}
```
## 🔹 GitLab CI — Slack Notification
You will learn how to **notify Slack in GitLab CI**.
```
stages:
  - build

variables:
  INSTITUTE_NAME: "Cloudops"
  SLACK_WEBHOOK: "https://hooks.slack.com/services/XXXXX/YYYYY/ZZZZZ"

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - git clone https://github.com/ashishcloudops01/sample-maven-project.git
    - cd sample-java-app
    - mvn clean package
    - curl -X POST -H 'Content-type: application/json' --data '{"text":"Build success for Cloudops"}' $SLACK_WEBHOOK
```
