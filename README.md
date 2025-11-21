# Project: Node-CICD-Demo with Jenkins
# Author: Aashish Chaudhary

**Goal:** Jenkins Fundamental Concepts Hands-on Learnings

---

## Overview
This Project completely focuses on the fundamental concepts of Jenkins and covers all the basic components of CICD under it. Some examples include Jenkins’ Architectures, Jobs, Builds, JenkinsFile, Pipelines, Plugins, and many more.

In this simple Node-application-Project, after the source code is pushed to the version control tool Git, the webhook under the repository triggers the Jenkins Build, and the Jenkins Tool automatically builds, tests, and deploys the Node application using the Jenkinsfile under whatever environment we ask it to build on. In this case, I have asked to build it on Kubernetes Cluster (K3s) setup, that I set up earlier to learn Github and Github Actions.

---

## Phase 1: Environment Setup

### EC2 instance (with Associated-Elastic IP) with K8S and Jenkins
For this particular project, I used the same EC2 instance with K3S set up on it. Now, I need the machine to host the Jenkins Server.

- I installed Java Development Kit (JDK-17) as Jenkins runs on Java.
- After adding up some repository on machines `/etc/apt/sources.list.d`, I installed Jenkins.

While setting Jenkins up, it needed an IP address (not changing frequently). That is why, I associated an Elastic IP address with my EC2 instance.

Inside the Jenkins GUI:

- I installed necessary plugins associated with Github, Docker, Git, Pipelines and many more.
- I also created the app in the slack’s url.
- I created the slack’s webhook that can be used for slack notifications.

---

## Phase 2: Project Setup
On the machine, I wrote:

- the simple node application
- test cases inside the test folder
- Dockerfile
- Jenkinsfile with the necessary ‘stages’, ‘actions’ and provided necessary credentials as Environment Variables
- .yml files for Kubernetes to use when the Jenkins deploy the application inside the EC2

I also initialized the project folder with `git init` command. And the project was ready.

For pushing it to the version control, I needed a repository in GitHub.

---

## Phase 2: Github Setup (Repo and Webhook)
First of all, I needed a place to store the codes. So, I created a repository with **“node-cicd-demo”** name in GitHub.

Next, I changed the remote GitHub link for ‘fetch and push’ to its URL.

After committing and pushing it to the main origin, the entire project was up in GitHub.

Now I needed the webhook to trigger the Jenkins build (the instance set up in my EC2). So, I created webhook with url:


