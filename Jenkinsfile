pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '/usr/local/maven/bin/mvn -B -DskipTests clean package'
            }
        }
        stage('Test') { 
            steps {
                sh '/usr/local/maven/bin/mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
        stage('OWASP Check') {
            steps {
                dependencyCheck additionalArguments: '--format HTML', odcInstallation: 'DP-Check'
            }
        }
        stage("Build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sq1') {
                sh "/usr/local/maven/bin/mvn clean package sonar:sonar -Dsonar.projectKey=Demo1 -Dsonar.projectName='Demo1' -Dsonar.host.url=http://172.18.25.204:9000 -Dsonar.token=sqp_cf8460affcf087fd3ceff9dbc623a99b9f4d2935"
              }
            }
        }
        stage("Quality Gate") {
            steps {
              timeout(time: 5, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
    }
}