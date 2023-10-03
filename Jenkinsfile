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
        IMAGE_NAME = 'nginx'
        IMAGE_TAG = 'latest'
    }
  stages {
    stage('Build') {
      steps {
        container('docker') {
          sh 'docker build -t $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG .'
        }
      }
    }
    stage('Login') {
      steps {
        container('docker') {
          sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_TOKEN'
      }
    }
    }
    stage('Push') {
      steps {
        container('docker') {
          sh 'docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG'
      }
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
