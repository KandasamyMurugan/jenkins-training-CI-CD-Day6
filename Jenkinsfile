pipeline {     
    agent {  label "linuxbuildnode"    }
   
    stages {
        // Step 1
        stage('SCM') {
                steps {
                    git branch: 'master', credentialsId: 'GITHUB_PASSWORD', url: 'git@github.com:KandasamyMurugan/jenkins-training-CI-CD-Day6.git'
                }        
        }
        // Step 2
        stage('Build by Maven') {
                steps {
                    sh 'mvn clean package'
                }
        }
        
        // Step 3
        stage('Build docker image') {
                steps {
                    sh "sudo docker build -t kmurugandocker/javaapp-day6:${BUILD_NUMBER} ."
                }
        }
        
        // Step 4
        stage('Push docker image') {
                steps {
                    withCredentials([string(credentialsId: 'Docker_hub_password', variable: 'VAR_FOR_DOCKERPASS')]) {
                    sh "sudo docker login -u kmurugandocker -p $VAR_FOR_DOCKERPASS"
                    }
                    sh "sudo docker push kmurugandocker/javaapp-day6:${BUILD_NUMBER}"
                }
        }
              
        // Step 5 
        stage('Deploy Java App in  Dev Env') {
                steps {
                        sh "sudo docker rm -f myjavaappdevenv"
                        sh "sudo docker run  -d -p 1222:8080 --name myjavaappdevenv kmurugandocker/javaapp-day6:${BUILD_NUMBER}"
                }
        }
        
        // Step 6  in  Redhat CLI 1 
        stage('Deploy Java in QA/Test Env') {
            steps {
                         sh "sudo docker rm -f myjavaappqatestenv"            
                         sh "sudo docker run  -d -p 1223:8080 --name myjavaappqatestenv kmurugandocker/javaapp-day6:${BUILD_NUMBER}"           
                    // }
            }
        }
            
        stage('QAT Test') {
            steps {
        	// bcz tomcat take some sec. to display data , so apply some delay here        
                retry(30) {
                    sh 'curl --silent http://34.238.240.228/java-web-app/ |  grep India'
                }   
            }
        }
        
        // Step  in Redhat 8 CLI 2
        stage('Deploy webAPP in Prod Env') {
            steps {
               
                         
                // }
                //sh "sudo docker rm -f myjavaappprodenv"
                sh "sudo docker run  -d -p 1224:8080 --name myjavaappprodenv kmurugandocker/javaapp-day6:${BUILD_NUMBER}"  
            }
        }
    }
}   

