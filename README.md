# Module 8: Build Automation & CI/CD with Jenkins
This repository contains all my work, notes, hands‑on exercises, and project files for **Module 8: Build Automation & Continuous Integration / Continuous Delivery (CI/CD) with Jenkins**. The goal of this module is to build a strong understanding of automated build processes, pipeline orchestration, and end‑to‑end CI/CD delivery using Jenkins.

## Notes
[Introduction to Build Automation](notes/Introduction.md)
[Introduction to Jenkins UI](notes/Introduction.md)
[Docker with Jenkins](notes/Introduction.md)
[Freestyle to Pipeline jobs](notes/Introduction.md)




<details>

<summary>Jenkins Shared Library</summary>

There are cases where an application will consist of multiple microservices which in turn might have its own pipelines along with its own Jenkinsfile. There could be cases where the logic (example build  image and deploy to registry) could be the same across multiple Jenkinsfile. 

To avoid code redundancy , we can use Jenkins shared library which is basically is its own repository and with shared logic which can be used by multiple pipelines.

- Extension to the Pipeline
- It has its own repository and written in Groovy
- Reference shared logic in Jenkinsfile

Another use, a company could have multipe projects with individual pipelines. They may not use the same tech stack but some of the logic is the same. For example, company wide Nexus repository and Slack channel. 

All of this code can be written in the shared library which avoids duplication of code among multiple pipelines.

Benefits

- Code reusability
- Easy maintenance
- Consistency and Standardization
- Faster Pipeline creation
- Improved collaboration

## Steps to create a Jenkins Shared Library
- Create repository
- Write the Groovy code
- Make the Shared Library available globally or for project
- Use the Shared Library in Jenkinsfile to extend the pipeline

## Structure of Shared Library

- vars folder
    - Functions that we call from Jenkinsfile
    - Each function/execution step is its own Groovy file
- src folder
    - Helper code
- resources folder
    - Use external libraries
    - Non-groovy files (example - SQL scripts)

</details>





## Projects
1. [Install Jenkins on DigitalOcean](https://github.com/bigtony79/jenkins-docker-digitalocean.git)
2. [Create a CI Pipeline with Jenkinsfile (Freestyle, Pipeline, Multibranch Pipeline)](https://github.com/bigtony79/jenkins-08-freesyle-pipeline-multibranch/tree/main)
3. [Jenkins Shared Library](https://github.com/bigtony79/jenkins-08-shared-library)
