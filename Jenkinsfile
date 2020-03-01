node {
    docker.image('maven:3-alpine').inside('-v $HOME/.m2:/root/.m2') {
       stage('SCM') {
        git 'https://github.com/kobibasson/course'
      }
      stage('SonarQube analysis') {
        withSonarQubeEnv(credentialsId: 'scanner', installationName: 'sonar') {
          sh 'mvn clean verify sonar:sonar'
        }
      }
      stage('Build War') {
        sh "mvn -pl web -am clean install"
      }
      stage('Push War to Nexus') {
        nexusArtifactUploader(
          nexusVersion: 'nexus3',
          protocol: 'http',
          nexusUrl: '172.31.5.214:8081',
          groupId: 'clinic.programming.time-tracker',
          version: '0.3.1',
          repository: 'maven-releases',
          credentialsId: 'nexus',
          artifacts: [
              [
              artifactId: 'time-tracker-web',
              classifier: '',
              file: "/var/jenkins_home/workspace/maven-push/?/.m2/repository/clinic/programming/time-tracker/time-tracker-web/0.3.1/time-tracker-web-0.3.1.war",
              type: 'war'
              ]
          ]
        )
      }
    }
    stage('Build Docker Image') {
        withDockerRegistry([ credentialsId: "nexus", url: "http://172.31.5.214:8083" ]) {
          tracker = docker.build("172.31.5.214:8083/automat-it/time-tracker-web:0.3.1")
          tracker.push()
        }
      }
}