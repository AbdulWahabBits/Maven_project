def skipRemainingStages = false
pipeline {
   
   agent none
   

    parameters {
         // string(name: 'tomcat_dev', defaultValue: '35.166.210.154', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'localhost', description: 'Production Server')
    }

    
tools {
    maven 'MyMaven'
  }
   

stages{
        stage('Code Check Out'){
		  agent {label 'Master'}  
		when {
                expression { !skipRemainingStages }
                     }
            steps {
		git url: "https://github.com/AbdulWahabBits/Maven_project.git1"
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
                     skipRemainingStages = true
                     //or
                    println "skipRemainingStages = ${skipRemainingStages}"
                    }
             }
            }
        }
	
	
	stage('Build'){
		  agent {label 'Dev'}  
            steps {
		bat label: '', script: 'mvn clean package'
                echo "Build successful";
		stash 'Build'    
            }
            post {
                success {
                    echo 'Now Archiving...'
                    //archiveArtifacts artifacts: '**/target/*.war'
                    archiveArtifacts artifacts: 'webapp/target\\*.war'
		 stash 'file'
                }
            }
        } 
		
		/*stage('Test') {
		     agent {label 'Dev'}
		     steps {
			     unstash 'source'
			    bat label: '', script: 'mvn test'
                            echo "test successful";
			 }
			 post {
			 success{
			  echo 'Now will deploy...'
			 }
			 failure{
			 echo 'test failed...'
			 }
			 }
			 } */

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Development'){
				agent {label 'Dev'}
                    steps {
			    unstash 'file'
			bat label: '', script: 'mvn clean package'
                        bat "copy webapp\\target\\*.war \"C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps\\*.war\""
                    }
                } 

               
                stage ("Deploy to Production"){
				agent {label 'Prod'}
                    steps {
                       timeout(time:5, unit:'DAYS'){input message:'Approve PRODUCTION Deployment?'
                        //sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
		      unstash 'file'
                       bat "copy webapp\\target\\*.war \"C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps\\*.war\""
                    }
                   }
                    post {
                      success {
                           echo 'Code deployed to Production.'
                         }
                     failure {
                              echo ' Deployment failed.'
                              
                              }
                        } //post
                   
                                }
              
            }
        }
  /*stage ('Start Tomcat Server') {
         sleep(time:5,unit:"SECONDS") 
         bat "${tomcatBin}\\startup.bat"
         sleep(time:100,unit:"SECONDS")
   }*/
   
   
   
   //end Brackets
    }
}
