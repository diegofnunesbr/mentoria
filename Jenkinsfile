pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
            - mountPath: /var/run/docker.sock
              name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock
        '''
    }
  }
    environment {
        DOCKERHUB_USERNAME = 'diegofnunesbr'
        DOCKERHUB_TOKEN = credentials('dockerhub')
        IMAGE_NAME = 'testing-image'
        IMAGE_TAG = 'latest'
        CONTAINER_NAME = 'nginx'
    }
  stages {
    stage('Build-Nginx') {
      steps {
        container('docker') {
          sh 'docker build -t nginx:latest .'
        }
      }
    }
    stage('Build-Docker') {
      steps {
        container('docker') {
          sh 'docker build -t $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG .'
        }
      }
    }
    stage('Login-Docker') {
      steps {
        container('docker') {
          sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_TOKEN'
      }
    }
    }
    stage('Push-Docker-to-DockerHub') {
      steps {
        container('docker') {
          sh 'docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG'
      }
    }
    }
    stage('Run') {
      steps {
        container('docker') {
          sh 'docker run -d -p 80:80 --name nginx:latest'
      }
    }
    }
    post {
      always {
        container('docker') {
          sh 'docker logout'
      }
      }
    }
}
