# Module 8: Build Automation & CI/CD with Jenkins
This repository contains all my work, notes, hands‑on exercises, and project files for **Module 8: Build Automation & Continuous Integration / Continuous Delivery (CI/CD) with Jenkins**. The goal of this module is to build a strong understanding of automated build processes, pipeline orchestration, and end‑to‑end CI/CD delivery using Jenkins.

## Notes

[Introduction to Build Automation](notes/Introduction.md)
[Introduction to Jenkins UI](notes/Introduction.md)
[Docker with Jenkins](notes/Introduction.md)
[Freestyle to Pipeline jobs](notes/Introduction.md)


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

### Freestyle jobs
Freestyle jobs are simple, UI‑driven job types designed for executing single steps in the build or deployment process.  
Examples include:  
* Running unit tests
* Building an application
* Deploying artifacts

To create a full workflow, multiple Freestyle jobs often need to be chained together, which becomes difficult to maintain as complexity grows. 

### Limitations of Freestyle jobs
Freestyle jobs have several drawbacks:  
* Limited functionality — best suited for basic tasks
* UI‑based configuration — not easily portable, version‑controlled, or reusable
* Restricted by plugin input fields — reduces flexibility
* Not ideal for complex workflows — complicated pipelines become hard to manage with chained jobs

To address these limitations, Jenkins introduced Pipeline jobs, which offer greater control, flexibility, and maintainability through script‑based configuration.

### Why Pipeline jobs are recommended
Pipeline jobs use Jenkinsfiles, allowing you to:
* Define pipelines as code
* Version‑control your CI/CD workflow
* Reuse and share code easily
* Build complex, multi‑stage pipelines
* Reduce dependency on UI clicks
* Improve reliability and repeatability

</details>
<details>
 
<summary>Jenkinsfile Syntax</summary>

- **Post attribute:**  
    - Execute some logic after all stages executed
    - For example, send an email after build is complete or failed
    
    ```jsx
    pipeline {
      agent any
    
      stages {
        stage("build") {
          steps {
            echo "building the application ..."
          }
        }
    
        stage("test") {
              steps {
                echo "testing the application..."
              }
        }
        
        stage("deploy") {
          steps {
            echo "deploying the application...."
          }
        }
    		post {
    			always {
    			//insert code here
    			}
    			failure {
    			//insert code here
      }
     } 
    }
    ```
    
- **Define conditionals for each stage**
    - You can define conditionals using "when" block
        
        ```jsx
        pipeline {
          agent any
        
          stages {
            stage("build") {
              steps {
                echo "building the application ..."
              }
            }
        
            stage("test") {
                  when {
        	          expression {
        		          BRANCH_NAME == 'dev'
        		          //This branch gets executed when the BRANCH_NAME is dev
        		          }
        		        }  
                  
                  steps {
                    echo "testing the application..."
                  }
            }
            
            stage("deploy") {
              steps {
                echo "deploying the application...."
              }
            }
          }
        }
        ```
        
- **Environmental variables**
    - Jenkins provide a list of environmental variables which can be referenced in your Jenkinsfile script.
    
    ```jsx
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	  
    
      stages {
        stage("build") {
          steps {
            echo "building the application ..."
            echo "building version ${NEW_VERSION}" //always needs to enclose in double quotes to access the variable
          }
        }
    
        stage("test") {
              steps {
                echo "testing the application..."
              }
        }
        
        stage("deploy") {
          steps {
            echo "deploying the application...."
          }
        }
      }
    }
    ```
    
- **Using credentials in Jenkinsfile and access them as environmental variable:**
    - Assuming that you saved the desired credentials in Jenkins as ‘server-credentials’ - you can access those credentials in Jenkinsfile by using the withCredentials function shown below
    
    ```jsx
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	  SERVER_CREDENTIALS = credentials('server-credentials')
    	}
    	  
    
      stages {
        stage("build") {
          steps {
            echo "building the application ..."
            echo "building version ${NEW_VERSION}" //always needs to enclose in double quotes to access the variable
          }
        }
    
        stage("test") {
              steps {
                echo "testing the application..."
              }
        }
        
        stage("deploy") {
          steps {
            echo "deploying the application...."
            withCredentials([
    		        usernamePassword(credentials:'server-credentials', usernameVariable: USER, passwordVariable: PWD))
    		   ]) {
    				    sh "some script ${USER} ${PWD}"
          }
        }
      }
    }
    ```
    
- **Tools attribute**
    - You can access build tools such as Maven, jdk, gradle using the tools attribute.
    
    ```jsx
    pipeline {
      agent any
      tools {
    	 // maven <name of tool configured in Jenkins> 
    	 maven "maven-3.9"
    	} 
      
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	  
    
      stages {
        stage("build") {
          steps {
            echo "building the application ..."
            echo "building version ${NEW_VERSION}" //always needs to enclose in double quotes to access the variable
          }
        }
    
        stage("test") {
              steps {
                echo "testing the application..."
              }
        }
        
        stage("deploy") {
          steps {
            echo "deploying the application...."
          }
        }
      }
    }
    ```
    
- **Parameters attribute: Types of parameters include**
    - string (name,  defaultValue, description)
    - choice(name, choices, description)
    - booleanParam(name, defaultValue, description)
    
    ```jsx
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	parameters  {
    		string(name:'VERSION', defaultValue:'', description: 'version to deploy on prod')
    		choice(name:'VERSION', choices: ['1.1.0','1.2.0','1.3.0'],description:'')
    		booleanParam(name: 'executeTests', defaultValue: true, description: '') 
    	  }
    
      stages {
        stage("build") {
          steps {
            echo "building the application ..."
            echo "building version ${NEW_VERSION}" //always needs to enclose in double quotes to access the variable
          }
        }
    
        stage("test") {
              when {
    	          expression {
    		          params.executeTests
    		         }
                        
              steps {
                echo "testing the application..."
              }
        }
        
        stage("deploy") {
          steps {
            echo "deploying the application...."
            echo "deploying version {params.VERSION}"
          }
        }
      }
    }
    ```
    
- **Using external Groovy script**
    - Good idea to put multiple scripts into a separate Goovy script to avoid clutter in the Jenkinsfile
    - All environmental variables in Jenkinsfile are available in the groovy script
    
    ```jsx
    // external Groovy script - script.groovy
    
    def buildApp() {
    	 echo "building the application ..."
       echo "building version ${NEW_VERSION}" //always needs to enclose in double quotes to access the variable
    }
    
    def testApp() {
    	echo 'testing the application'
    }
    
    def deployApp() {
    	echo "deploying the application...."
      echo "deploying version {params.VERSION}"
      }
    }
    return this
    ```
    
    ```jsx
    def gv
    
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	parameters  {
    		string(name:'VERSION', defaultValue:'', description: 'version to deploy on prod')
    		choice(name:'VERSION', choices: ['1.1.0','1.2.0','1.3.0'],description:'')
    		booleanParam(name: 'executeTests', defaultValue: true, description: '') 
    	  }
    
      stages {
        
    	  stage("init") {
    		  steps {
    			  script {
    				  gv = load "script.groovy"
    				  }
    				 }
    				} 
    			   
        stage("build") {
          steps {
    	      script {
    		      gv.buildApp()
    		      }
    	        
                 }
        }
    
        stage("test") {
              when {
    	          expression {
    		          params.executeTests
    		         }
                        
              steps {
                script {
    	            gv.testApp()
    	            }       
                }
              }
        
        stage("deploy") {
          steps {
    			     script {
    				     gv.deployApp()
            }
        }
      }
    }
    ```
    
- **Input parameter**
    - Typical scenario involves allowing a user to select an environment for deployment after a successful build→run tests→build image→push to private repo stages.
    - Another scenario to allow user to input a version for build
    
    ```jsx
    def gv
    
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	parameters  {
    		string(name:'VERSION', defaultValue:'', description: 'version to deploy on prod')
    		choice(name:'VERSION', choices: ['1.1.0','1.2.0','1.3.0'],description:'')
    		booleanParam(name: 'executeTests', defaultValue: true, description: '') 
    	  }
    
      stages {
        
    	  stage("init") {
    		  steps {
    			  script {
    				  gv = load "script.groovy"
    				  }
    				 }
    				} 
    			   
        stage("build") {
          steps {
    	      script {
    		      gv.buildApp()
    		      }
    	        
                 }
        }
    
        stage("test") {
              when {
    	          expression {
    		          params.executeTests
    		         }
                        
              steps {
                script {
    	            gv.testApp()
    	            }       
                }
              }
        
        stage("deploy") {
          input {
    	      message "Select the environment to deploy to"
    	      ok "Done
    	      parameters {
    		      choice(name:'ENV', choices: ['dev','staging','production'],description:'')
    				}
          
          steps {
    			     script {
    				     gv.deployApp()
    				     echo "Deploying to ${ENV}" //local scope only.
            }
        }
      }
    }
    ```
    ![alt text](images/image1.png)
    ![alt text](images/image2.png)
    
- **To allow multiple input selections (for example, deploy to 2 different systems)**
    
    ```jsx
    def gv
    
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	parameters  {
    		string(name:'VERSION', defaultValue:'', description: 'version to deploy on prod')
    		choice(name:'VERSION', choices: ['1.1.0','1.2.0','1.3.0'],description:'')
    		booleanParam(name: 'executeTests', defaultValue: true, description: '') 
    	  }
    
      stages {
        
    	  stage("init") {
    		  steps {
    			  script {
    				  gv = load "script.groovy"
    				  }
    				 }
    				} 
    			   
        stage("build") {
          steps {
    	      script {
    		      gv.buildApp()
    		      }
    	        
                 }
        }
    
        stage("test") {
              when {
    	          expression {
    		          params.executeTests
    		         }
                        
              steps {
                script {
    	            gv.testApp()
    	            }       
                }
              }
        
        stage("deploy") {
          input {
    	      message "Select the environment to deploy to"
    	      ok "Done
    	      parameters {
    		      choice(name:'ONE', choices: ['dev','staging','production'],description:'')
    		      choice(name:'TWO', choices: ['dev','staging','production'],description:'')
    				}
          
          steps {
    			     script {
    				     gv.deployApp()
    				     echo "Deploying to ${ONE}" //local scope only.
    				     echo "Deploying to ${TWO}" //local scope only.
            }
        }
      }
    }
    ```
    ![alt text](images/image3.png)
    
- **Another common way to use input attribute is to assign it to the environment variable within the script block**
    
    ```jsx
    def gv
    
    pipeline {
      agent any
      environment {
    	  NEW_VERSION = '1.3.0' // environmental variable
    	}
    	parameters  {
    		string(name:'VERSION', defaultValue:'', description: 'version to deploy on prod')
    		choice(name:'VERSION', choices: ['1.1.0','1.2.0','1.3.0'],description:'')
    		booleanParam(name: 'executeTests', defaultValue: true, description: '') 
    	  }
    
      stages {
        
    	  stage("init") {
    		  steps {
    			  script {
    				  gv = load "script.groovy"
    				  }
    				 }
    				} 
    			   
        stage("build") {
          steps {
    	      script {
    		      gv.buildApp()
    		      }
    	        
                 }
        }
    
        stage("test") {
              when {
    	          expression {
    		          params.executeTests
    		         }
                        
              steps {
                script {
    	            gv.testApp()
    	            }       
                }
              }
        
        stage("deploy") {
                
          steps {
    			     script {
    				     env.ENV = input message: "Select the environment to deploy to", ok "Done", parameters: [choice(name:'ONE', choices: ['dev','staging','production'],description:''))]
    				     gv.deployApp()
    				     echo "Deploying to ${ENV}" //local scope only.
    				     
            }
        }
      }
    }
    ```
</details>  
<details>
<summary>Multibranch Pipeline</summary>  

 - In modern Git workflows, teams work on **feature**, **bugfix**, and **hotfix** branches instead of committing directly to `main`.
- You want **fast feedback** (build + test) on every branch, and **deployment** to happen only from trusted branches like `main`.
- Jenkins **Multibranch Pipeline** automates this by **discovering branches/PRs** and creating a **dedicated pipeline per branch** based on the branch’s own `Jenkinsfile`.

### What Multibranch Pipelines Solve

1. **Branch Isolation**
    - Every branch gets its **own pipeline** run with its **own Jenkinsfile** (so behavior can vary by branch).
    - Ensures features are validated early without affecting `main`.
2. **Automatic Discovery**
    - Jenkins scans the repo (via webhook or polling), **detects new branches/PRs**, and instantly creates pipelines—no manual job creation.
3. **Policy Enforcement**
    - You can enforce rules like **“test on all branches, deploy only from main”** via `when { branch 'main' }` or naming conventions (`release/*`, `hotfix/*`).
4. **Event-Driven CI**
    - On **push**, the branch pipeline builds and tests.
    - On **merge to main**, Jenkins runs **test → build → deploy**.
 
</details>

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
