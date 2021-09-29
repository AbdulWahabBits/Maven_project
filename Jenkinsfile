def runRemainingStages = true


pipeline {
   
   agent none
   

        
tools {
    maven 'MyMaven'
  }
   

stages{
        stage('Code Check Out'){
		  agent {label 'Master'}  
		when {
                expression { runRemainingStages }
                     }
            steps {
		git url: "https://github.com/AbdulWahabBits/Maven_project.git"
                stash 'source'    
		    
            }
            post {
                success {
		    echo "Code Checkout successful";
                    echo 'Now Archiving...'
                    //archiveArtifacts artifacts: '**/target/*.war'
                    archiveArtifacts artifacts: 'webapp/target\\*.war'
		 stash 'file'
                        }
		failure {
                script{
                     runRemainingStages = false
                     println "runRemainingStages = ${runRemainingStages}"
                    }
             }
            }
        }
	
	
	stage('Build'){
		  agent {label 'Master'}  
		when {
                expression { runRemainingStages }
                     }
            steps {
		bat label: '', script: 'mvn clean package'
               
		stash 'Build'    
            }
            post {
                success {
		     echo "Build successful";
		    mail(from: "awahabjenkins@gmail.com", 
         			  to: "2021ht66017@wilp.bits-pilani.ac.in", 
         			  subject: "That build was successful.",
          			   body: "That branch build was successful :)")
                    echo 'Now Archiving...'
                    //archiveArtifacts artifacts: '**/target/*.war'
                    archiveArtifacts artifacts: 'webapp/target\\*.war'
		 stash 'file'
			
                }
		    failure {
                script{
                     runRemainingStages = false
                     println "runRemainingStages = ${runRemainingStages}"
		     }
			       mail(from: "awahabjenkins@gmail.com", 
         			  to: "2021ht66017@wilp.bits-pilani.ac.in", 
         			  subject: "That branch build failed.",
          			   body: "The Branch Pipeline failed :(")
		    }
            }
        } 
		
	stage('Test'){
		  agent {label 'Master'}  
		when {
                expression { runRemainingStages }
                     }
            steps {
		bat label: '', script: 'mvn test'
                   }
            post {
                success {
		     echo "Test successful";
		    mail(from: "awahabjenkins@gmail.com", 
         			  to: "2021ht66017@wilp.bits-pilani.ac.in", 
         			  subject: "That test in branch was successful.",
          			   body: "That test in branch was successful :)")
                    
                }
		    failure {
                script{
                     runRemainingStages = false
                     println "runRemainingStages = ${runRemainingStages}"
		     }
			       mail(from: "awahabjenkins@gmail.com", 
         			  to: "2021ht66017@wilp.bits-pilani.ac.in", 
         			  subject: "That Test in branch failed.",
          			   body: "The Branch Pipeline failed :(")
		    }
            }
        }

        stage ('Deployments'){
		when {
                expression { runRemainingStages }
                     }
            parallel{
                stage ('Deploy to Development'){
				agent {label 'Dev'}
                    steps {
			    unstash 'file'
			bat label: '', script: 'mvn clean package'
                        bat "copy webapp\\target\\*.war \"C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps\\*.war\""
                         }
			post {
                      success {
                           echo 'Code deployed to Development.'
			      mail(from: "awahabjenkins@gmail.com", 
         			  to: "2021ht66017@wilp.bits-pilani.ac.in", 
         			  subject: "Deployed to Development BraNCH",
          			   body: "You can now test the web application :)")
                         }
                     failure {
                              script{
                    		 runRemainingStages = false
                     		 println "runRemainingStages = ${runRemainingStages}"
                    		}
			     mail(from: "awahabjenkins@gmail.com", 
         			  to: "2021ht66017@wilp.bits-pilani.ac.in", 
         			  subject: "Deployment to Development Branch Failed",
          			   body: "Please check your Development branch stage in pipeline:)")
                              }
                        } //post
                } 

               
                              
            }
        }
 
    }
}
