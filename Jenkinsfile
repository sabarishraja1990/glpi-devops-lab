
pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        GLPI_SOURCE = 'glpi-source'
        GLPI_IMAGE = 'glpi-ci-test'
    }

    stages {
        stage('Checkout Pipeline') {
            steps {
                echo 'Checking out the pipeline repository'
                checkout scm
            }
        }

        stage('Checkout GLPI Source') {
            steps {
                dir("${GLPI_SOURCE}") {
                    git branch: '12.0/bugfixes',
                        url: 'https://github.com/glpi-project/glpi.git'
                }
            }
        }

        stage('Validate Source') {
            steps {
                dir("${GLPI_SOURCE}") {
                    sh 'test -f composer.json'
                    sh 'test -f .docker/app/Dockerfile'
                    sh 'echo "GLPI source validation passed"'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${GLPI_SOURCE}") {
                    sh '''
                        docker build \
                          --build-arg PHP_VERSION=8.4 \
                          -f .docker/app/Dockerfile \
                          -t glpi-ci-test:${BUILD_NUMBER} \
                          .
                    '''
                }
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker image inspect glpi-ci-test:${BUILD_NUMBER}'
                echo 'GLPI test image built successfully'
            }
        }
    }

    post {
        success {
            echo 'GLPI CI pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
