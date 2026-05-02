cat > Jenkinsfile <<'EOF'
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

        stage('Create Build Artifact') {
            steps {
                sh '''
                echo "Creating build artifact..."
                mkdir -p build
                cp app.py README.md build/
                zip -r ${ARTIFACT_NAME} build/
                ls -lh
                '''
            }
        }

        stage('Upload Artifact to JFrog') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'jfrog-creds',
                    usernameVariable: 'JFROG_USER',
                    passwordVariable: 'JFROG_PASS'
                )]) {
                    sh '''
                    echo "Uploading artifact to JFrog..."
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
            echo 'Build artifact uploaded to JFrog successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
EOF
