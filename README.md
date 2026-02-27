# Module 8: Build Automation & CI/CD with Jenkins
This repository contains all my work, notes, hands‑on exercises, and project files for **Module 8: Build Automation & Continuous Integration / Continuous Delivery (CI/CD) with Jenkins**. The goal of this module is to build a strong understanding of automated build processes, pipeline orchestration, and end‑to‑end CI/CD delivery using Jenkins.

## Notes
<details>

<summary>Intro to Build Automation</summary>
Build automation is a **process that automatically compiles, packages, tests, and prepares software** so developers don’t have to do those steps manually. It’s a core part of modern DevOps and CI/CD pipelines.

Build automation uses tools and scripts to automatically:
- Fetch code from version control
- Compile source code
- Run automated tests
- Package the application (JAR, container image, ZIP etc.)
- Run quality checks (linting, security scans, etc.)
- Produce build artifacts
- Trigger deployments

It replaces manual commands with repeatable, automated tasks.
 ![alt text](images/Build-automation.png)


### Why it matters

Build automation helps teams to:

- Eliminate human error
- Ensure repeatability
- Speed up delivery
- Enable CI/CD
- Improve code quality
- Support DevOps at scale

### Common Build Automation Tools

#### General CI/CD

- GitHub Actions
- Jenkins
- GitLab CI
- Azure DevOps Pipelines

#### Language-Specific Build Tools

- Java: Maven, Gradle
- JavaScript: npm, yarn
- Python: pip, tox

</details>

<details>

<summary>Introduction to Jenkins UI</summary>

## Roles for Jenkins

### Jenkins Administrator - Operations or DevOps Teams

- Administers and manages Jenkins
- Setup Jenkins cluser
- Install plugins
- Backup Jenkins Data

### Jenkins User - Developer or DevOps teams

- Creating the actual jobs to run workflows


</details>

<details>

<summary>Docker with Jenkins</summary>

Since Jenkins is running as a Docker container on the host droplet, we need to mount the host machine’s Docker runtime directory into the Jenkins container. This allows Jenkins to execute Docker commands (such as building and pushing images) directly on the host.

</details>

<details>
 
<summary>Freestyle to Pipeline jobs</summary>

## Freestyle jobs
Freestyle jobs are simple, UI‑driven job types designed for executing single steps in the build or deployment process.  
Examples include:  
* Running unit tests
* Building an application
* Deploying artifacts

To create a full workflow, multiple Freestyle jobs often need to be chained together, which becomes difficult to maintain as complexity grows. 

## Limitations of Freestyle Jobs
Freestyle jobs have several drawbacks:  
* Limited functionality — best suited for basic tasks
* UI‑based configuration — not easily portable, version‑controlled, or reusable
* Restricted by plugin input fields — reduces flexibility
* Not ideal for complex workflows — complicated pipelines become hard to manage with chained jobs

To address these limitations, Jenkins introduced Pipeline jobs, which offer greater control, flexibility, and maintainability through script‑based configuration.

</details>

## Projects
1. [Install Jenkins on DigitalOceans](https://github.com/bigtony79/jenkins-docker-digitalocean.git)
2. Create a CI Pipeline with Jenkinsfile (Freestyle, Pipeline, Multibranch Pipeline)
