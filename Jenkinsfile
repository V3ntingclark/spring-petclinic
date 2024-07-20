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
        script {
          script {
            def scannerHome = tool 'SonarQube'
            withSonarQubeEnv('SonarQube') {
              withCredentials([usernamePassword(credentialsId: 'sonar-credentials-id', usernameVariable: 'SONARQUBE_USERNAME', passwordVariable: 'SONARQUBE_PASSWORD')]) {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=spring-petclinic -Dsonar.login=${SONARQUBE_USERNAME} -Dsonar.password=${SONARQUBE_PASSWORD}"
              }
            }
          }
        }

      }
    }

    stage('Unit Test') {
      steps {
        junit '**/target/surefire-reports/TEST-*.xml'
        sh 'mvn test'
      }
    }

    stage('Package') {
      steps {
        sh 'mvn package'
        archiveArtifacts '**/target/*.jar'
      }
    }

  }
}