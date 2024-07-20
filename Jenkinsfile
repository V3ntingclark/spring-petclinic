pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git(url: 'https://github.com/V3ntingclark/spring-petclinic', branch: 'main')
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean compile'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        sh 'def scannerHome = tool \'SonarQubeScanner\' withSonarQubeEnv(\'SonarQube\') {     sh "${scannerHome}/bin/sonar-scanner" }'
      }
    }

    stage('Unit Test') {
      steps {
        junit '**/target/surefire-reports/TEST-*.xml'
        sh 'mnv test'
      }
    }

    stage('Package') {
      steps {
        sh 'mnv package'
        archiveArtifacts '**/target/*.jar'
      }
    }

  }
}