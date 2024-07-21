pipeline {
    agent any
    tools {
        jdk 'JDK 17'
        maven 'Maven 3'
    }
    environment {
        SONARQUBE_URL = 'http://3.143.110.235:9000'
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        git url: "https://${GITHUB_TOKEN}@github.com/V3ntingclark/spring-petclinic.git", branch: 'main'
                    }
                }
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
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Deploy to Nexus') {
            steps {
                script {
                    def nexusRepository = "maven-releases"
                    def nexusUrl = "http://3.143.110.235:8081"
                    def groupId = "org.springframework.samples"
                    def artifactId = "petclinic"
                    def version = "1.0-SNAPSHOT"
                    def packaging = "jar"
                    def artifactPath = "target/${artifactId}-${version}.${packaging}"

                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: nexusUrl,
                        groupId: groupId,
                        version: version,
                        repository: nexusRepository,
                        credentialsId: 'nexus-credentials',
                        artifacts: [
                            [
                                artifactId: artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: packaging
                            ]
                        ]
                    )
                }
            }
        }
    }
}
