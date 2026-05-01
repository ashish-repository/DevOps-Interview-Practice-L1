# Day 16 — Ansible Roles, DynamoDB, StatefulSet, Service Check

## 🔹 Ansible — Create Role Structure

# Students must create:
# roles/
#   Cloudops/
#     tasks/main.yml
#     handlers/main.yml
#     templates/
#     vars/main.yml

Example task file:

---
- name: Install httpd
  yum:
    name: httpd
    state: present


## 🔹 Terraform — DynamoDB Table

resource "aws_dynamodb_table" "CloudopsTable" {
  name           = "CloudopsTrainingTable"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "ID"

  attribute {
    name = "ID"
    type = "S"
  }

  tags = {
    Name = "Cloudops-DDB"
  }
}


## 🔹 Kubernetes — StatefulSet

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: Cloudops-db
spec:
  serviceName: "Cloudops"
  replicas: 2
  selector:
    matchLabels:
      app: Cloudops-db
  template:
    metadata:
      labels:
        app: Cloudops-db
    spec:
      containers:
        - name: db
          image: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "Cloudops123"


## 🔹 Shell Script — Check Service Running

#!/bin/bash

SERVICE="sshd"

if systemctl is-active --quiet $SERVICE; then
  echo "$SERVICE is running"
else
  echo "$SERVICE is NOT running"
fi


# Artifacts Promotion

## 🔹 Jenkins Pipeline — Promote Artifacts
You will learn how to **promote build artifacts from dev to staging**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "Cloudops"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Promote') {
            steps {
                input message: "Promote artifacts to staging?"
                sh 'echo "Promoting artifacts for $INSTITUTE_NAME"'
            }
        }
    }
}

## 🔹 GitLab CI — Artifacts Promotion
You will learn how to **promote artifacts manually using GitLab CI manual job**.

stages:
  - build
  - promote

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - mvn clean package
  artifacts:
    paths:
      - target/*.jar
    expire_in: 1 week

promote:
  stage: promote
  image: alpine:latest
  script:
    - echo "Promoting artifacts for Cloudops"
  when: manual
