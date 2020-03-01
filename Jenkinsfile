node {
    docker.image('maven:ibmjava-alpine').inside {
       stage('SCM') {
        git 'https://github.com/kobibasson/course'
      }
      stage('SonarQube analysis') {
        withSonarQubeEnv(credentialsId: 'scanner-token', installationName: 'sonar') {
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
          nexusUrl: 'nexus:8081',
          groupId: 'clinic.programming.time-tracker',
          version: '0.3.1',
          repository: 'maven-releases',
          credentialsId: 'nexus',
          artifacts: [
              [
              artifactId: 'time-tracker-web',
              classifier: '',
              file: '${JENKINS_HOME}/.m2/repository/clinic/programming/time-tracker/time-tracker-web/0.3.1/time-tracker-web-0.3.1.war',
              type: 'war'
              ]
          ]
        )
      }
    }
}