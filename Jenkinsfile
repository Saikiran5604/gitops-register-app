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
                // FIXED: Complete repository URL layout mapping cleanly to your fork path
                git branch: 'main', credentialsId: 'github', url: 'https://github.com'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   echo "=== Before Update ==="
                   cat deployment.yaml
                   
                   sed -i "s|register-app-pipeline:.*|register-app-pipeline:${env.IMAGE_TAG}|g" deployment.yaml
                   
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
                   git commit -m "Updated Deployment Manifest to tag ${env.IMAGE_TAG} [skip ci]"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    // FIXED: Replaced malformed @://github.com with a valid target path template 
                    // FIXED: Kept inside single quotes ('...') so the variables expand natively in the shell environment
                    sh 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@://github.com main'
                }
            }
        }
    }
}
