## 1. What is a Job?
A Job (often called a Project) is a user-defined description of work. It is the "Plan." You define what to do, where the code is, and when it should run.

- Configurational: It contains the Git repository URL, the environment variables, and the specific shell commands or scripts to execute.
- Persistent: A Job stays in Jenkins forever (until you delete it), even if it hasn't run yet.

## 2. What is a Build?
A Build is a single execution of a Job. It is the "Action." If a Job is the blueprint for a car, a Build is one specific car coming off the assembly line.

- Result-Oriented: Every build has a specific outcome, such as SUCCESS, FAILURE, or ABORTED.
- Numbered: Jenkins tracks every build with a unique ID (e.g., Build #1, Build #2).
- Historical: It stores the console logs, artifacts (like a .jar or .zip file), and test results from that specific run.

## 3. Internal Working: Job vs. Build
When you click "Build Now" or a webhook triggers a process, Jenkins follows a specific internal lifecycle to move from a static Job to an active Build.

The Lifecycle
- Triggering: The process starts via a manual click, a code commit (Git hook), or a scheduled timer (Cron).
- The Queue: Jenkins places the request into the Build Queue. If all "Executors" (workers) are busy, the build waits here.
- Resource Allocation: The Jenkins Master identifies an available Executor. This can be on the Master itself or a remote Agent/Node (like a separate Linux server or a Docker container).
- Workspace Creation: Jenkins creates a dedicated directory on the Agent called a Workspace. It then "clones" your code from the repository into this folder.
- Execution: Jenkins runs the steps defined in your Job (e.g., mvn clean install or docker build).
- Post-Build Actions: After the commands finish, Jenkins performs cleanup, archives the resulting files, and sends notifications (like an email or Slack message).
- Finalization: The Build status is recorded in the Jenkins filesystem, and the Workspace is usually cleared or left for the next run.

### Behind the Scenes: The File System
On the server, Jenkins stores these internally in the $JENKINS_HOME directory:

- Jobs: Located in /jobs/[job_name]/config.xml.
- Builds: Located in /jobs/[job_name]/builds/[build_number]/. This folder contains the build.xml (metadata) and the log file.

## 4. Jenkins Freestyle Project
A Freestyle Project is the "legacy" way of using Jenkins. It is entirely GUI-based.

- How it works: You fill out web forms to define your build. You click buttons to add "Build Steps" (like "Execute Shell") and "Post-build Actions."
- Pros: Very easy for beginners; no coding required.
- Cons: Hard to version control. If someone changes a setting in the UI, there is no "history" of who did what. It doesn't scale well for complex workflows.

## 5. Jenkins Pipeline (The Modern Standard)
A Pipeline is a suite of plugins that supports implementing and integrating continuous delivery pipelines into Jenkins. It uses a file called a Jenkinsfile which is stored in your Git repository.

- Declarative Pipeline
This is the most popular type of Pipeline today. It uses a strict, pre-defined structure that is easy to read.

  - Syntax: It always starts with the keyword pipeline { }.
  - Structure: It is broken down into stages and steps.
  - Benefit: It has built-in error checking and is very readable for teams.

- Scripted Pipeline
This was the original way to write Pipelines.

  - Syntax: It starts with node { }.
  - Nature: It uses a lot of Groovy code and has very few restrictions.
  - Benefit: Extremely powerful and flexible, but much harder to write and maintain.

## 6. What is an Agent?
In Jenkins, an Agent is a machine or container that connects to the Jenkins Master and executes the tasks defined in a Job.

- Dynamic Agents: In modern setups (like using your MacBook with UTM or Docker), Jenkins can spin up a container as an agent, run the build, and then delete the container immediately. This keeps your environment "clean."

## 7. Multi-node Architecture
This refers to a Jenkins setup where one Master controls multiple Workers/Agents.

#### Why use it? 
- 1. Parallelism: You can run a Java build on Agent A and a Python test on Agent B at the same time.
- 2. Environment Specificity: You might have a "Windows Node" for .NET apps and a "Linux Node" for Docker apps.
- 3. Reliability: If one node crashes, the others keep working.

## 8. Groovy & The Groovy Sandbox
Since Jenkins Pipelines are based on the Groovy programming language, security is a major concern.

#### What is Groovy?
Groovy is a Java-syntax-compatible object-oriented programming language for the Java platform. Jenkins uses it to allow complex logic (if-else statements, loops) inside your build scripts.

#### The Groovy Sandbox
The Sandbox is a security mechanism.

- The Problem: Since Groovy is a powerful language, a developer could technically write a script that deletes all files on the Jenkins server or steals passwords.
- The Solution: Jenkins runs these scripts in a "Sandbox" that restricts what the script can do.
- Approval: If you try to use a "dangerous" command (like accessing the local file system), Jenkins will block it. An administrator must manually go to "Manage Jenkins" > "In-process Script Approval" to allow that specific command.
