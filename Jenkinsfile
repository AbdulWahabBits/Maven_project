
pipeline {
   
     agent any
   

    parameters {
         // string(name: 'tomcat_dev', defaultValue: '35.166.210.154', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'localhost', description: 'Production Server')
    }

    
tools {
    maven 'MyMaven'
  }
   

stages{
        stage('Build'){
		      agent any
            steps {
                bat label: '', script: 'mvn clean package'
                echo "Build successful";
                
            }
            post {
                success {
                    echo 'Now Archiving...'
                    //archiveArtifacts artifacts: '**/target/*.war'
                    archiveArtifacts artifacts: 'webapp/target\\*.war'
                }
            }
        }
		
		stage('Test') {
		     agent {label 'Dev'}
		     steps {
			    bat label: '', script: 'mvn test'
                            echo "test successful";
			 }
			 /*post {
			 success{
			  echo 'Now will deploy...'
			 }
			 failure{
			 echo 'test failed...'
			 }
			 }*/
			 }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Development'){
				agent {label 'Dev'}
                    steps {
                        bat "copy webapp\\target\\*.war \"C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps\\dev.war\""
                    }
                } 

               
                stage ("Deploy to Production"){
				agent {label 'Prod'}
                    steps {
                       //timeout(time:5, unit:'DAYS'){input message:'Approve PRODUCTION Deployment?'
                        //sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                       bat "copy webapp\\target\\*.war \"C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps\\*.war\""
                    }
                   // }
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
