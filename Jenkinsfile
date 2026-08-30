pipeline {
    agent { label "jenkins-agent" }
    
    environment {
        APP_NAME = "register-app-pipeline"
        // Ensure IMAGE_TAG is passed down cleanly or dynamically defined
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
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Saikiran5604/gitops-register-app'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "=== Before Update ==="
                    cat deployment.yaml
                    
                    # Updates the image matching your APP_NAME with the dynamic IMAGE_TAG
                    sed -i "s|${env.APP_NAME}:.*|${env.APP_NAME}:${env.IMAGE_TAG}|g" deployment.yaml
                    
                    echo "=== After Update ==="
                    cat deployment.yaml
                """   
            }
        }

        stage("Push the changed deployment file to git") {
            steps {
                sh """ 
                    git config --global user.name "Jenkins Agent"
                    git config --global user.email "jenkins@devops.internal"
                    git add deployment.yaml
                    git commit -m "Update deployment manifest to tag ${env.IMAGE_TAG} [skip ci]"
                """

                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/Saikiran5604/gitops-register-app main"
                }
            }
        }
    }
}
