pipeline {
    agent any

    environment {
        NEXUS_USER = credentials('nexus-credentials').username
        NEXUS_PASS = credentials('nexus-credentials').password
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/timothy-toweh/ansible-project.git'
            }
        }
        stage('Configuration') {
            steps {
                sh 'ansible-playbook -i hosts.ini 01.config.yml'
            }
        }
        stage('Build') {
            steps {
                sh 'ansible-playbook -i hosts.ini 02.build.yml'
            }
        }
        stage('Test') {
            steps {
                sh 'ansible-playbook -i hosts.ini 03.test.yml'
            }
        }
        stage('Upload to Nexus') {
            steps {
                sh '''
                    ansible-playbook -i hosts.ini 04.upload.yml \
                    -e nexus_username=${NEXUS_USER} \
                    -e nexus_password=${NEXUS_PASS}
                '''
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                sh 'ansible-playbook -i hosts.ini 05.deploy.yml'
            }
        }
    }

    post {
        success {
            mail to: 'timothytoweh1@gmail.com',
                 subject: 'Jenkins CI-CD Successful',
                 body: 'Jenkins CI-CD Job was successful, YAAAY'
        }
        failure {
            mail to: 'timothytoweh1@gmail.com',
                 subject: 'Jenkins CI-CD Failed',
                 body: 'Jenkins CI-CD job failed, sorry'
        }
    }
}
