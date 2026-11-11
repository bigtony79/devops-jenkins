# Jenkins Shared Library

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

