# **Home Assignment - Bank Leumi**

In this assignment, I have created a Jenkins pipeline that builds my application, pushes it to an ECR repository, and installs it on a Kubernetes cluster using Helm.

## **Project Structure**

The repository contains the following directories and files:

- **`hello-world-app/`**  
  This directory contains the Helm chart for deploying the Hello World application.

- **`jenkins/`**  
  This directory contains the Helm chart for deploying the Jenkins instance.

- **`Dockerfile`**  
  The Dockerfile for building the Hello World application image.

- **`Jenkinsfile`**  
  The Jenkins pipeline script that automates the CI/CD process.

- **`hello_world.py`**  
  A simple Python Flask application.
