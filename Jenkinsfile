pipeline {

    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    environment {

        APP_NAME       = "express-login-ui"
        IMAGE_NAME     = "durgesh2003/express-login-ui"

        CONTAINER_PORT = "3000"
        HOST_PORT      = "3000"
    }


    stages {


        stage('Clone Source') {

            steps {

                git branch: 'main',
                url: 'https://github.com/Durgesh-0403/express-login-ui'

            }
        }



        stage('Cleanup Old Container & Image') {

            steps {

                sh '''
                    echo "Cleaning old container"

                    docker rm -f $APP_NAME || true

                    echo "Cleaning old image"

                    docker rmi $IMAGE_NAME || true
                '''

            }
        }



        stage('Build Docker Image') {

            steps {

                sh '''
                    echo "Building Docker Image"

                    docker build -t $IMAGE_NAME .
                '''

            }
        }




        stage('Docker Login') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'DockerHubCreds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                        echo "Logging into Docker Hub"

                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" \
                        --password-stdin
                    '''

                }

            }
        }




        stage('Push Docker Image') {

            steps {

                sh '''

                    echo "Pushing Docker Image"

                    docker push $IMAGE_NAME

                '''

            }

        }




        stage('Run Container') {

            steps {

                sh '''

                    echo "Starting Container"


                    docker rm -f $APP_NAME || true


                    docker run -d \
                    --name $APP_NAME \
                    -p $HOST_PORT:$CONTAINER_PORT \
                    $IMAGE_NAME


                '''

            }

        }




        stage('Health Check') {

            steps {

                sh '''

                    echo "Checking Application"

                    sleep 10


                    curl -f http://localhost:$HOST_PORT/hello


                    echo "Application Running Successfully"

                '''

            }

        }



    }



    post {


        success {

            echo "Pipeline executed successfully!"

        }



        failure {

            echo "Pipeline failed!"

        }



        always {

            sh '''

            echo "Docker logout"

            docker logout || true

            '''

            echo "Pipeline Completed"

        }


    }

}
