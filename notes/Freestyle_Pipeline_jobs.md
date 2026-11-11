# Freestyle to Pipeline jobs

## Freestyle jobs
Freestyle jobs are simple, UI‑driven job types designed for executing single steps in the build or deployment process.  
Examples include:  
* Running unit tests
* Building an application
* Deploying artifacts

To create a full workflow, multiple Freestyle jobs often need to be chained together, which becomes difficult to maintain as complexity grows. 

## Limitations of Freestyle jobs
Freestyle jobs have several drawbacks:  
* Limited functionality — best suited for basic tasks
* UI‑based configuration — not easily portable, version‑controlled, or reusable
* Restricted by plugin input fields — reduces flexibility
* Not ideal for complex workflows — complicated pipelines become hard to manage with chained jobs

To address these limitations, Jenkins introduced Pipeline jobs, which offer greater control, flexibility, and maintainability through script‑based configuration.

## Why Pipeline jobs are recommended
Pipeline jobs use Jenkinsfiles, allowing you to:
* Define pipelines as code
* Version‑control your CI/CD workflow
* Reuse and share code easily
* Build complex, multi‑stage pipelines
* Reduce dependency on UI clicks
* Improve reliability and repeatability
