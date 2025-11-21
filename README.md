# Project: Node-CICD-Demo with Jenkins

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

Now I needed the webhook to trigger the Jenkins build (the instance set up in my EC2). So, I created webhook with url:http://Jenkins’IP/webhook/.


Inside the Jenkins’ Manage Jenkins section, I added the credentials for GitHub, Slack, and Docker hub.

Now, on every new “Commit and Push” event, the Github’s Webhook triggers the Jenkins Build event. The Jenkins uses the Jenkinsfile and carries out all the tasks that’s defined in it.

---

## Workflow
The entire project’s workflow is based on Jenkinsfile.

- In my Jenkins file, I have asked Jenkinsfile to take any agent that is available.
- Under my case, it would be the same EC2 instance available and I want it to be both master and slave agent.
- Under the next section, I have defined environment variables for my agent node.
- I have used my credentials ‘ids’ stored in Jenkin’s as parameters to these environment variables.
- Under it, I have defined seven stages and actions inside it.

### First 6 Stages
For the first 6 stages:

- the agent pulls the code from the repo’s url
- then installs necessary tools (npm and tests: defined test case)
- the agent builds the docker image using Dockerfile
- and pushes the image to the dockerhub registry

### Deployment
Now for the Deployment:

- the agent needs a place to deploy the yml file
- It creates namespace, if it’s not available
- If it’s available, it uses the existing one
- Then runs the `kubectl apply` command

### Final Section
For the last section, it defines what to output under the successful event and unsuccessful event.

---

## Issues and Troubleshooting

### 1. Jenkins-setup
During the Jenkins Setup, the GPG key was not properly added. The old ways of adding the Jenkins GPG key on Linux was deprecated and hence, the machine was not able to add new repository into its `/etc/apt/sources.list.d/`.

Later when approached with new steps, it did not work on that too. Later, going through attention to details, I saw missing `/` at the end of URL. This way, both Jenkins key and sources list were added.

---

### 2. Application Source Code Commit to Wrong Repository
I had added the other repository’s URL for my GitHub And Actions Learning Project. I had not changed my machine’s git’s associated link to the new one.

I actually ran the command to link it to the new repository’s URL. But for some reason, it did not take into effect. And when I committed it, it pushed it to the other repository.

Later I had to revert the commit to previous commit using its hashcode. And finally changing the ‘fetch and push’ link to new repository, I pushed the code.

---

### 3. Jenkins User not using ‘docker command’
After the Push event, the GitHub webhook triggers the Jenkins build event. When Jenkins would carry out the Jobs in the Jenkinsfile, it would fail during the Docker Image build.

Digging out the reason behind this, I came up with the main issue of having my Jenkins user in my EC2 not added into the ‘sudo’ user group.

Later, Adding the Jenkins user from output of `/etc/passwd` file, to the sudo group using: 'sudo usermod -aG sudo <username>' command. 
It solved the issue.

---

### 4. Default Namespace Usage in K3S Cluster
The Jenkinsfile was triggering the Jenkins Build to deploy the applications in my already setup K3S cluster.

But the issue was it was using the default namespace. For this reason, I could not see my pods and deployments.

I could see my previous deployments but the newer deployments.

Later, I saw, I was missing `namespace:` field in my .yml files’ metadata. Later, I added the environment for namespace in the Jenkinsfile and solved the problem.

---

### 5. Application Deployments Crash
After the successful CICD from the Jenkins’ build event, my application was still crashing and not up for the testing. It was crashing for some reason.

Upon checking the logs of the pods and details of the Deployments, I found the issue in my `package.json` file. I had forgotten to add the dependencies for the ‘express’ environment, my app would require to run.

After adding the dependencies, the problem was solved.

---

### 6. Test Case Failed
The test case defined in the `/test` folder was failing. I was using the jest testcase.

However, I was not defining it inside the `package.json` file. I was using default test statement and because of this, it was causing issue during Jenkins’ Testing stage.

