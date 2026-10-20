
pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Starting the glpi-devops-lap'
                checkout scm
            }
        }

        stage('Validate Repository') {
            steps {
                echo 'Checking pipeline files'

                sh 'git --version'
                sh 'test -f Jenkinsfile'
            }
        }

        stage('Pipeline Summary') {
            steps {
                echo 'Repository checkout and validation completed'
                echo 'Next: Docker image build and automated deployment'
            }
        }
    }

    post {
        success {
            echo 'GLPI DevOps pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
