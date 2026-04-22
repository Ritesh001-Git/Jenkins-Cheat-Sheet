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

### Jenkins Internal Architecture (Master-Agent)
Jenkins does not usually run these builds on the same server where the UI is hosted. Doing so would crash the system during heavy tasks. Instead, it uses a Distributed Architecture.

#### The Jenkins Master (The Brain)
The Master is the central "Control Tower." Its jobs include:

- Scheduling: Deciding which job runs when.
- Dispatching: Sending the build instructions to an available Agent.
- Monitoring: Watching the Agents to see if they are healthy.
- Recording: Saving the logs and build history.

#### The Jenkins Agents (The Muscle)
Agents are separate machines (Linux, macOS, or Windows) or Docker Containers that do the actual "heavy lifting."

- They receive commands from the Master.
- They execute the shell scripts or build commands.
 -Once the task is done, they send the results back to the Master and wait for the next task.

### The "In-Depth" Internal Working
When a build starts, here is the technical sequence of events:

- The Request: A trigger hits the Master.
- The Allocation: The Master looks at its "Executors." If you have a MacBook and a Linux server connected as Agents, the Master decides which one is best suited for the task.
- Remoting: The Master communicates with the Agent via the Jenkins Remoting Protocol (usually over SSH or JNLP).
- The Workspace: The Agent creates a temporary folder. It clones the Git repository here.
- The Execution:
  - If you are using a Jenkinsfile, the Master reads the "Stages" (Build -> Test -> Deploy).
  - It sends these commands one by one to the Agent.
- The Feedback Loop: As the Agent runs a command (e.g., docker build), it streams the console output back to the Master in real-time so you can watch it in your browser.
- Clean up: Once finished, the Agent stays online, but the Workspace is either cleared or saved for the next incremental build.
