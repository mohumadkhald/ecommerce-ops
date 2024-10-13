pipeline
{
    agent any

    environment {
        APP_NAME = "ecommerce_ci_cd"

    }

    parameters {
        string(name: "REPLICAS", defaultValue: '1', description: 'Num of the Application replicas')
    }

    stages {
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'Github', url: 'https://github.com/mohumadkhald/ecommerce-ops'
            }
        }

        stage("update App Deployment"){
            steps {
                sh '''
                cat ./dev/deployment.yml
                sed -i "s/replicas:.*/replicas: ${REPLICAS}/g" ./dev/deployment.yml
                sed -i "s/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g" ./dev/deployment.yml
                cat ./dev/deployment.yml
                '''
            }
        }
        stage("Commit and Push Changes") {
            steps {
                withCredentials([usernamePassword(credentialsId: "Github", usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        git config user.email "jenkins@yourdomain.com"
                        git config user.name "Jenkins"

                        git add ./dev/deployment.yml
                        git commit -m "Update ${APP_NAME} image to ${IMAGE_TAG}"
                        git push https://${USER}:${PASS}@github.com/mohumadkhald/ecommerce-ops.git main
                    '''
                }
            }
        }

    }
}
