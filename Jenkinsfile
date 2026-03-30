pipeline {
    agent any

    environment {
        IMAGE_NAME = "apoorvar12/board-game"
        TAG = "${BUILD_NUMBER}"
        FULL_IMAGE = "${IMAGE_NAME}:${TAG}"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/apoorvaramesh11/BoardGame.git', branch: 'main'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'DOCKERHUB_ID',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    echo "Docker Login Successful"
                    '''
                }
            }
        }

        stage('Building application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Building docker image') {
            steps {
                sh 'docker build -t ${FULL_IMAGE} .'
            }
        }

        stage('Push to dockerhub') {
            steps {
                sh 'docker push ${FULL_IMAGE}'
            }
        }

        stage('Deploy to Kubernetes') {
  	    steps {
       		 sh """
		 kubectl get nodes
        	 sed -i 's|image:.*|image: ${FULL_IMAGE}|' manifests/deployment-service.yaml
        	 kubectl apply -f manifests/deployment-service.yaml
			 echo "After update:"
			 cat deployment.yaml
       		 """
    	}
	}	
    }
}
