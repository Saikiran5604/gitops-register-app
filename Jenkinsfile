pipeline {
    agent { label "jenkins-agent" }

    environment {
        APP_NAME = "register-app-pipeline"
        IMAGE_TAG = "1.0.0-${BUILD_NUMBER}"
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
                    url: 'https://github.com/Saikiran5604/gitops-register-app'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "=== Before Update ==="
                    cat deployment.yaml

                    sed -i "s|register-app-pipeline:.*|register-app-pipeline:${IMAGE_TAG}|g" deployment.yaml

                    echo "=== After Update ==="
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {

                sh """
                    git config --global user.name "Saikiran5604"
                    git config --global user.email "reddysaikiran257@gmail.com"

                    git add deployment.yaml

                    git commit -m "Updated Deployment Manifest to tag ${IMAGE_TAG} [skip ci]"
                """

                withCredentials([
                    gitUsernamePassword(
                        credentialsId: 'github',
                        gitToolName: 'Default'
                    )
                ]) {
                    sh 'git push origin main'
                }
            }
        }
    }
}
