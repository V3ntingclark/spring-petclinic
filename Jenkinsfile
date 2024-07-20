pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/V3ntingclark/spring-petclinic'
      }
    }

    stage('Build') {
      steps {
        pwsh 'mvn clean compile'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        pwsh 'def scannerHome = tool \'SonarQubeScanner\' withSonarQubeEnv(\'SonarQube\') {     sh "${scannerHome}/bin/sonar-scanner" }'
      }
    }

    stage('Unit Test') {
      steps {
        pwsh 'mnv test'
        junit '**/target/surefire-reports/TEST-*.xml'
      }
    }

    stage('Package') {
      steps {
        pwsh 'mnv package'
      }
    }

  }
}