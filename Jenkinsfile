pipeline {
    environment {
        registry = 'mohammedyb/pmapi'
        dockerHubCreds = 'docker_hub'
        dockerImage = ''
        deploymentFile = 'ProjectManagementAPI/k8s/deployment.yml'
    }
    agent any
    stages {
        stage('Test') {
            when {
                branch 'feature/*'
            }
        steps {
            withMaven {
                cleanWs()
                sh 'mvn test'
            }
        }
    }
    stage('Build') {
        when {
            branch 'main'
        }
        steps {
            withMaven {
                sh 'mvn -f ProjectManagementAPI/pom.xml clean install'
                sh 'mvn -f ProjectManagementAPI/pom.xml clean package -DskipTests'
            }
        }
    }
    stage('Docker Build') {
        when {
            branch 'main'
        }
        steps {
            script {
                echo "$registry:$currentBuild.number"
                dockerImage = docker.build ("$registry", "-f ProjectManagementAPI/Dockerfile .")
            }
        }
    }
    stage('Docker Deliver') {
        when {
            branch 'main'
        }
        steps {
            script {
                docker.withRegistry('', dockerHubCreds) {
                    dockerImage.push("$currentBuild.number")
                    dockerImage.push("latest")
                }
            }
        }
    }

    }
}
