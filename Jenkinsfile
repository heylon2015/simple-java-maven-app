def mylabel = "jenkins-slave-${UUID.randomUUID().toString()}"
pipeline {
  agent {
    kubernetes {
      label mylabel
      defaultContainer 'maven'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: helm
    image: dtzar/helm-kubectl:2.11.0
    command: ['cat']
    tty: true
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
    stage('Checkout') {
      steps {
        git branch: 'master',
          url: 'https://github.com/heylon2015/simple-java-maven-app.git'
      }
    }
    stage('Validate') {
      steps {
        container('maven') {
          sh 'mvn validate'
        }
      }
    }
    stage('Compile') {
      steps {
        container('maven') {
          sh 'mvn clean compile'
        }
      }
    }
    stage('Test') {
      steps {
        container('maven') {
          sh 'mvn clean test'
        }
      }
    }
    stage('Package') {
      steps {
        container('maven') {
          sh 'mvn -B -DskipTests clean package'
        }
      }
    }
    stage('helmcheck') {
      steps {
        container('helm') {
          sh """
          helm create test
          pwd
          whoami
          """
        }
      }
    }
    stage('Push') {
      steps {
        container('maven') {
          sh """
          mkdir -p /data/workspace/${JOB_NAME}
          cp ${WORKSPACE}/target/*.jar /data/workspace/${JOB_NAME}
          """
        }
      }
    }
  }
}
