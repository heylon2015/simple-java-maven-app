pipeline {
  agent {
    kubernetes {
      label 'maven-docker-${UUID.randomUUID().toString()}'
      defaultContainer 'maven'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:3.3.9-jdk-8-alpine
    command: ['cat']
    tty: true
    volumeMounts:
    - name: task-pv-storage
      mountPath: /root/.m2/repository
      readOnly: false
    - name: workspace-pv-storage
      mountPath: /data/workspace
      readOnly: false
  - name: docker
    image: docker:1.13
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
    - name: workspace-pv-storage
      mountPath: /data/workspace
      readOnly: false
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
  - name: task-pv-storage
    persistentVolumeClaim:
      claimName: maven-repo
  - name: workspace-pv-storage
    persistentVolumeClaim:
      claimName: jenkins-workspace
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
