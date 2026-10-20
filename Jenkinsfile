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
                checkout scm
                echo 'Pipeline repository checkout complete'
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
                    sh 'test -f front/login.php'
                    sh 'test -f .docker/app/Dockerfile'
                    echo 'GLPI source validation passed'
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
                          -t ${GLPI_IMAGE}:${BUILD_NUMBER} \
                          .
                    '''
                }
            }
        }

        stage('Test PHP Syntax') {
            steps {
                dir("${GLPI_SOURCE}") {
                    sh '''
                        set -eu
                        CONTAINER="glpi-php-test-${BUILD_NUMBER}"
                        trap 'docker rm -f "$CONTAINER" >/dev/null 2>&1 || true' EXIT

                        docker create --name "$CONTAINER" \
                          --entrypoint sh \
                          ${GLPI_IMAGE}:${BUILD_NUMBER} \
                          -c "sleep 120"

                        docker cp front/login.php "$CONTAINER":/tmp/login.php
                        docker start "$CONTAINER" >/dev/null
                        docker exec "$CONTAINER" php -l /tmp/login.php
                    '''
                }
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker image inspect ${GLPI_IMAGE}:${BUILD_NUMBER}'
                echo 'Docker image verification passed'
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
