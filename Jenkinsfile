pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'http://http://43.205.237.46:3000/Mohamed-Kababi/Jenkins-repo.git'
            }
        }
        
        stage('Run Ansible Playbook') {
            steps {
            	script {
                	ansiblePlaybook {
            	    	credentialsId: "Apache-server",
                		disableHostKeyChecking: true,
                		ansiblePlaybook colorized: true,
                		installation: 'Ansible',
                		playbook: '/var/lib/jenkins/workspace/Gogs-Apache-server/Ansible/InstallApache.yml',
                		inventory: '/var/lib/jenkins/workspace/Gogs-Apache-server/Ansible/inventory'
                    }
                }    
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-nginx-server .'
                sh 'docker save my-nginx-server > my-nginx-server.tar'
                archiveArtifacts artifacts: 'my-nginx-server.tar', fingerprint: true
            }
        }

        stage('Send Email Notification') {
            steps {
                script {
                    def deployGroupUsers = sh(script: 'getent group deployG | cut -d: -f4', returnStdout: true).trim()
                    def currentDate = new Date().format("yyyy-MM-dd HH:mm:ss")
                    mail to: 'your_email@example.com',
                         subject: "Jenkins Pipeline Execution - ${currentDate}",
                         body: """
                         Pipeline execution status: ${currentBuild.currentResult}
                         Users in deployG group: ${deployGroupUsers}
                         Date and time of execution: ${currentDate}
                         Path to Docker image: ${env.WORKSPACE}/my_image.tar
                         """
                }
            }
        }
    }
}
