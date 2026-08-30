pipeline {
    agent { label "jenkins-agent" } // Matches your active lowercase agent label
    
    environment {
        APP_NAME = "register-app-pipeline"
        
        // CRUCIAL ADDITION: Your main build pipeline passes IMAGE_TAG down.
        // We define a fallback dynamic evaluation here so the sed command doesn't evaluate to empty!
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
                // Swapped to point directly to your personal repository fork
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Saikiran5604/gitops-register-app'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   echo "=== Before Update ==="
                   cat deployment.yaml
                   
                   # Using alternative delimiter '|' to safely handle colon replacements in string expansion
                   sed -i "s|${env.APP_NAME}:.*|${env.APP_NAME}:${env.IMAGE_TAG}|g" deployment.yaml
                   
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
                    // This explicitly gives Git the correct path to your GitHub repository fork
                    sh 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@://github.com main'
                }
            }
        }



    }
}
