# Jenkins

## What is CI and CD?

Think of CI/CD as an automated assembly line. In the past, developers wrote code for months, then spent weeks trying to "merge" it all together—which usually caused a lot of bugs.

### CI (Continuous Integration)
- The practice of merging all developer code changes into a central repository multiple times a day. Each merge is automatically built and tested to catch errors early.

### CD (Continuous Delivery/Deployment)
- Delivery: Your code is always in a "ready-to-ship" state. A human clicks a button to send it to production.
- Deployment: The process is 100% automated. Every change that passes tests goes straight to the live users.

### The Universal CI/CD Workflow
Regardless of the tool you use, the "Universal Workflow" follows these steps:

- Source: Developer pushes code to a repository (like GitHub).
- Build: The system compiles the code and gathers dependencies (creating a .jar, .exe, or Docker image).
- Test: Automated scripts run to ensure the new code didn't break anything (Unit, Integration, and UI tests).
- Release/Deploy: The "Artifact" (the finished product) is moved to a server where users can access it.

### The High-Level CI/CD Architecture
The architecture is divided into four main stages. Think of this as the "Universal Workflow" in action:

#### Source Stage (The Trigger):

- Developers push code to Git (GitHub/GitLab).
- Jenkins is connected to Git via a Webhook. As soon as a "Push" happens, Git sends a signal to Jenkins: "Hey, there's new code. Start the engine."

#### Build Stage (The Factory):

- Jenkins pulls the code into a Workspace.
- It uses tools like Maven (for Java) or npm (for Node.js) to compile the code and download dependencies.
- Goal: Create an Artifact (a .jar file or a Docker Image).

#### Test Stage (The Quality Gate):

- Jenkins runs automated tests. If a single test fails, the "Build" is marked as FAILED, and the pipeline stops immediately. This prevents broken code from moving forward.

#### Deploy Stage (The Delivery):

- The successful artifact is pushed to a registry (like Docker Hub or AWS ECR) and then deployed to a server (like an EC2 instance or a Kubernetes cluster).

## What is Jenkins and why are we using it?

Jenkins is an open-source automation server that automates various stages of the software development lifecycle. It supports building, testing, and deploying applications across multiple environments, making it a critical component in CI/CD pipelines.

### Why use it?
Before Jenkins, a developer had to manually compile code, manually run tests on their machine, and manually FTP files to a server. Jenkins "listens" for changes and does all of this for you.
- Extensibility: It has over 1,500+ plugins. If you want to connect to AWS, Docker, Slack, or Jira, there’s a plugin for it.
- Automation: It turns human-error-prone tasks into repeatable, "one-click" (or zero-click) processes.

### The Universal Work of Jenkins (The "Orchestrator")
Jenkins doesn't usually "do" the work itself; it commands other tools to do it. It acts as the Conductor of an Orchestra:

- The Trigger: Jenkins sees a change in Git.
- The Command: Jenkins tells Maven or Gradle to "Build the project."
- The Verification: Jenkins tells JUnit or Selenium to "Run the tests."
- The Shipment: Jenkins tells Docker or Ansible to "Deploy this to the cloud.

## Internal Logic: Master and Agents
To handle heavy workloads, Jenkins uses a Distributed Architecture:

- Master (The Brain): Handles the GUI, keeps track of configurations, and schedules jobs.
- Agents (The Muscle): These are separate machines (or containers) that actually run the heavy build/test tasks. This keeps the Master from crashing when 100 people are building at once.
