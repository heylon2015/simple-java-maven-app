/**
 * This pipeline describes a multi container job, running Maven and Docker builds
 */

def label = "maven-${UUID.randomUUID().toString()}"

podTemplate(label: label, yaml: """
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
    volumeMounts:
    - name: workspace-pv-storage
      mountPath: /data/workspace
      readOnly: false
  volumes:
  - name: task-pv-storage
    persistentVolumeClaim:
      claimName: maven-repo 
  - name: workspace-pv-storage
    persistentVolumeClaim:
      claimName: jenkins-workspace 
"""
  ) {

  node(label) {
    stage('Build a Maven project') {
      git branch: 'master',
        url: 'https://github.com/heylon2015/simple-java-maven-app.git'
      container('maven') {
        sh """
        mvn -B -DskipTests clean package
        mkdir -p /data/workspace/${JOB_NAME}/
        cp ${WORKSPACE}/target/*.jar /data/workspace/${JOB_NAME}/
        """
      }
    }
    stage('Test a Maven project') { 
        sh """
        mvn test
        """ 
        post {
           always {
               junit 'target/surefire-reports/*.xml' 
           }
        }
     }
  }
}
