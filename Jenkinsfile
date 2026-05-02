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

        stage('Verify Files') {
            steps {
                sh '''
                echo "Checking project files..."
                pwd
                ls -la
                '''
            }
        }

        stage('Build Artifact') {
            steps {
                sh '''
                echo "Creating build folder..."
                rm -rf build
                mkdir build

                echo "Copying files..."
                cp app.py build/
                cp README.md build/

                echo "Creating zip artifact..."
                zip -r ${ARTIFACT_NAME} build/

                echo "Artifact created:"
                ls -lh ${ARTIFACT_NAME}
                '''
            }
        }

        stage('Upload Build to JFrog') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'jfrog-creds',
                    usernameVariable: 'JFROG_USER',
                    passwordVariable: 'JFROG_PASS'
                )]) {
                    sh '''
                    echo "Uploading artifact to JFrog Artifactory..."

                    curl -u $JFROG_USER:$JFROG_PASS \
                    -T ${ARTIFACT_NAME} \
                    ${JFROG_URL}/${REPO_NAME}/${ARTIFACT_NAME}

                    echo "Upload completed."
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Build artifact uploaded to JFrog.'
        }
        failure {
            echo 'FAILED: Check Jenkins console output.'
        }
    }
}
