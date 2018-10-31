pipeline {
  agent {
    kubernetes {
      label 'mypod'
      customWorkspace '/tmp/'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  containers:
  - name: maven
    image: maven:alpine
    command:
    - cat
    tty: true
  - name: busybox
    image: busybox
    command:
    - cat
    tty: true
"""
    }
  }
  stages {
    stage('Run maven') {
      steps {
        container('maven') {
          sh 'mvn -version'
          sh "echo workspace dir is ${pwd()}"
        }
        container('busybox') {
          sh '/bin/busybox'
        }
      }
    }
  }
}
