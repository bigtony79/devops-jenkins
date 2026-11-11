# Jenkinsfile Syntax

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
