pipeline{
    agent any

    environment{
        IMAGE_NAME = 'apoorvar12/board-game'
        TAG = '${BUILD_NUMBER}'
        FULL_IMAGE = '${IMAGE_NAME}:${TAG}'
    }

    stages{
        stage('Checkout'){
            steps{

                git url: 'https://github.com/apoorvaramesh11/BoardGame.git', branch: 'main'

            }
        }
        stage('building application'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Building docker image'){
            steps{
            sh 'docker build -t ${FULL_IMAG} .'
            }
        }
        stage('Push to dockerhub'){
            steps{
                
                echo 'Pushing the image to DockerHub'
                withCredentials([usernamePassword(
                    credentialsId: 'DOCKER_ID',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    echo "Login Successful"
                    docker push ${FULL_IMAGE}
                    '''
                }
            }
        }
        stage('Creating container'){
            steps{
                sh 'docker run -d -p 8081:8080 ${FULL_IMAGE}'
            }
        }
    }
}
