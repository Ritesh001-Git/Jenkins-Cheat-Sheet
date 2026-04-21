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
