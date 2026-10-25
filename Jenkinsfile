
pipeline {
    agent any

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-21-openjdk-amd64'
        PATH = "/usr/lib/jvm/java-21-openjdk-amd64/bin:${env.PATH}"
        SONAR_ORGANIZATION = 'rajukunta57'
        SONAR_PROJECT_KEY = 'RajuKunta57_jenkins-tomcat-cicd-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                sh 'mvn -B clean verify'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'sonarcloud-token',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
                    sh '''
                        mvn -B org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                          -Dsonar.organization=$SONAR_ORGANIZATION \
                          -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                          -Dsonar.host.url=https://sonarcloud.io \
                          -Dsonar.token="$SONAR_TOKEN"
                    '''
                }
            }
        }

        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.war',
                                 fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build, analysis, and WAR archiving completed.'
        }
        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
