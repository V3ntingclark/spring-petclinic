pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git(branch: 'main', url: 'https://github.com/V3ntingclark/spring-petclinic')
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
          def scannerHome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
          withSonarQubeEnv('SonarQube') {
            withCredentials([usernamePassword(credentialsId: 'sonar-credentials-id', usernameVariable: 'SONARQUBE_USERNAME', passwordVariable: 'SONARQUBE_PASSWORD')]) {
              withEnv(["SONAR_SCANNER_OPTS=-Dsonar.login=${SONARQUBE_USERNAME} -Dsonar.password=${SONARQUBE_PASSWORD}"]) {
                sh """
                ${scannerHome}/bin/sonar-scanner \
                -Dsonar.projectKey=spring-petclinic \
                -Dsonar.host.url=${SONARQUBE_URL} \
                -Dsonar.java.binaries=target/classes \
                -X
                """
              }
            }
          }
        }

      }
    }

    stage('Unit Test') {
      post {
        always {
          junit '**/target/surefire-reports/TEST-*.xml'
        }

      }
      steps {
        sh 'mvn test'
      }
    }

    stage('Package') {
      post {
        success {
          archiveArtifacts(artifacts: '**/target/*.jar', allowEmptyArchive: true)
        }

      }
      steps {
        sh 'mvn package'
      }
    }

  }
  tools {
    jdk 'JDK 17'
  }
  environment {
    SONARQUBE_URL = 'http://3.143.110.235:9000'
  }
}