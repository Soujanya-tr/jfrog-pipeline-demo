pipeline {
    agent any

    environment {
        JFROG_URL = 'http://localhost:8082/artifactory'
        REPO_NAME = 'jenkins-builds'
        ARTIFACT_NAME = "jfrog-demo-${BUILD_NUMBER}.zip"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Artifact') {
            steps {
                sh '''
                rm -rf build
                mkdir build

                cp app.py build/
                cp README.md build/

                zip -r ${ARTIFACT_NAME} build/
                ls -lh ${ARTIFACT_NAME}
                '''
            }
        }

        stage('Upload to JFrog') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'jfrog-creds',
                    usernameVariable: 'JFROG_USER',
                    passwordVariable: 'JFROG_PASS'
                )]) {
                    sh '''
                    curl -u $JFROG_USER:$JFROG_PASS \
                    -T ${ARTIFACT_NAME} \
                    ${JFROG_URL}/${REPO_NAME}/${ARTIFACT_NAME}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Artifact uploaded to JFrog.'
        }
        failure {
            echo 'FAILED: Check logs.'
        }
    }
}
