pipeline {
  agent {
    kubernetes {
      yaml """
kind: Pod
spec:
  containers:
  - name: python
    image: python:3.12-slim
    command: ["sleep"]
    args: ["99d"]
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command: ["sleep"]
    args: ["99d"]
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: dockerhub-config
        items:
          - key: .dockerconfigjson
            path: config.json
"""
    }
  }
  parameters {
    string(name: 'DOCKER_IMAGE', defaultValue: 'tu-usuario/ejemplo-jenkins-kaniko', description: 'Nombre de la imagen en DockerHub')
    string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Tag de la imagen')
  }
  stages {
    stage('Test') {
      steps {
        container('python') {
          sh 'pip install -r requirements.txt pytest'
          sh 'pytest'
        }
      }
    }
    stage('Build and Push') {
      steps {
        container('kaniko') {
          sh "sh build.sh ${params.DOCKER_IMAGE} ${params.IMAGE_TAG}"
        }
      }
    }
  }
}
