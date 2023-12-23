pipeline {     
    agent {  label "linuxbuildnode"    }
    environment {
        DOCKERHUB_CREDENTIALS=credentials('Docker_hub_password')
    }
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
                    sh "sudo docker build -t kmurugandocker/javaapp-day6:latest ."
                }
        }
        
        // Step 4
        stage('Docker login') {
                steps {
                                    
                    sh 'echo $DOCKERHUB_CREDENTIALS | docker login -u kmurugandocker --password-stdin'
                }
        }
        // Step 5
        stage('Push docker image') {
                steps {
                    sh 'docker push kmurugandocker/javaapp-day6:latest'
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
                    // sshagent(['QA_ENV_SSH_CRED']) {
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp webdevprashant/javaapp-day6:${BUILD_NUMBER}"
            sh "sudo docker rm -f myjavaappqatestenv"            
            sh "sudo docker run  -d -p 1223:8080 --name myjavaappqatestenv webdevprashant/javaapp-day6:${BUILD_NUMBER}"           
                    // }
            }
        }
            
        stage('QAT Test') {
            steps {
        	// bcz tomcat take some sec. to display data , so apply some delay here        
                retry(30) {
                    sh 'curl --silent http://192.168.43.56:1223/java-web-app/ |  grep India'
                }   
            }
        }
        
        // Step  in Redhat 8 CLI 2
        stage('Deploy webAPP in Prod Env') {
            steps {
               
            //   sshagent(['QA_ENV_SSH_CRED']) {                    
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp webdevprashant/javaapp-day6:${BUILD_NUMBER}"                   
                // }
                sh "sudo docker rm -f myjavaappprodenv"
                sh "sudo docker run  -d -p 1224:8080 --name myjavaappprodenv webdevprashant/javaapp-day6:${BUILD_NUMBER}"  
            }
        }
    }
}   

