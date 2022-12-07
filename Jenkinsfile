pipeline {
    
    agent {
        label "linuxbuildnode"
    }
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/Kira-0007/jenkins-docker-maven-java-webapp.git'
            }
            
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t bunny001/javaweb:${BUILD_TAG} ."
            }
        }
        
        stage('Push Image to Dcoker Hub') {
            steps {
                
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASS_CODE')]) {
    // some block
                sh "sudo docker login -u bunny001 -p $DOCKER_HUB_PASS_CODE"
}
                sh "sudo docker push bunny001/javaweb:${BUILD_TAG}"
            }
        }
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run -d -p 8080:8080 --name myjavaapp bunny001/javaweb:${BUILD_TAG}"
            }
        }
        
        stage('Deploy webAPP in QA/TestEnv') {
            steps {
                sshagent(['QA_ENV_SSH_CRED']) {
    // some block
    
                sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.251.102 sudo docker rm -f myjavaapp"
                sh "ssh ec2-user@13.233.251.102 sudo docker run -d -p 8080:8080 --name myjavaapp bunny001/javaweb:${BUILD_TAG}"
}
            }
        }
        /*
        stage('QAT Test') {
            steps {
                sh 'curl --silent http://52.66.20.45:8080/java-web-app/ | grep India'
            }
        }
        */
        
        stage('approval') {
            steps {
                
            
                script {
                    Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                    echo 'userInput: ' + userInput

                    if(userInput == true) {
                        // do action
                    } else {
                        // not do action
                        echo "Action was aborted."
                    }
            
                
                }
            }
        }
        
        stage('Deploy webAPP in Prod Env') {
            steps {
                sshagent(['QA_ENV_SSH_CRED']) {
    // some block
    
                sh "ssh -o StrictHostKeyChecking=no ec2-user@15.206.212.248 sudo docker rm -f myjavaapp"
                sh "ssh ec2-user@15.206.212.248 sudo docker run -it -d -p 8080:8080 --name myjavaapp bunny001/javaweb:${BUILD_TAG}"
}
            }
        }
    }
}
