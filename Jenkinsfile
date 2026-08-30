pipeline {
    agent { label "jenkins-agent" }

    environment {
        APP_NAME  = "register-app-pipeline"
        IMAGE_TAG = "1.0.0-${BUILD_NUMBER}"
        GIT_REPO  = "https://github.com/Saikiran5604/gitops-register-app.git"
    }

    stages {

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main',
                    credentialsId: 'github',
                    url: "${GIT_REPO}"
            }
        }

        stage("Test GitHub Authentication") {
            steps {
                withCredentials([
                    gitUsernamePassword(
                        credentialsId: 'github',
                        gitToolName: 'Default'
                    )
                ]) {
                    sh '''
                        echo "Testing GitHub authentication..."

                        git ls-remote \
                            https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Saikiran5604/gitops-register-app.git \
                            HEAD
                    '''
                }
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "========================================"
                    echo "Before Update"
                    echo "========================================"

                    cat deployment.yaml

                    echo ""
                    echo "Updating image tag to: ${IMAGE_TAG}"

                    sed -i "s|register-app-pipeline:.*|register-app-pipeline:${IMAGE_TAG}|g" deployment.yaml

                    echo ""
                    echo "========================================"
                    echo "After Update"
                    echo "========================================"

                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {

                sh """
                    git config user.name "Saikiran5604"
                    git config user.email "reddysaikiran257@gmail.com"

                    git add deployment.yaml

                    git commit -m "Updated Deployment Manifest to tag ${IMAGE_TAG} [skip ci]" || true
                """

                withCredentials([
                    gitUsernamePassword(
                        credentialsId: 'github',
                        gitToolName: 'Default'
                    )
                ]) {
                    sh '''
                        echo "========================================"
                        echo "Git Remote"
                        echo "========================================"

                        git remote -v

                        echo ""
                        echo "========================================"
                        echo "Pushing to GitHub"
                        echo "========================================"

                        git push \
                            https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Saikiran5604/gitops-register-app.git \
                            HEAD:main
                    '''
                }
            }
        }
    }
}
