
stage('Test PHP Syntax') {
    steps {
        dir("${GLPI_SOURCE}") {
            sh '''
                set -eu

                CONTAINER="glpi-php-test-${BUILD_NUMBER}"

                cleanup() {
                    docker rm -f "$CONTAINER" >/dev/null 2>&1 || true
                }
                trap cleanup EXIT

                docker create --name "$CONTAINER" \
                  --entrypoint sh \
                  ${GLPI_IMAGE}:${BUILD_NUMBER} \
                  -c "sleep 120"

                docker cp front/login.php "$CONTAINER":/tmp/login.php

                docker start "$CONTAINER" >/dev/null

                docker exec "$CONTAINER" \
                  php -l /tmp/login.php
            '''
        }
    }
}
