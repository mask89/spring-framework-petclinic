pipeline {
    agent any
    stages {

        stage('Compile') {
            steps {
                sh '/usr/local/maven/bin/mvn clean compile'
            }
        }
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
                sh "/usr/local/maven/bin/mvn clean package sonar:sonar -Dsonar.projectKey=DevSecOps-Pipeline-V1 \
  -Dsonar.projectName='DevSecOps-Pipeline-V1' \
  -Dsonar.host.url=http://172.18.25.204:9000 \
  -Dsonar.token=sqp_8d4e9cc2f3ad961aa03a71fadab9e5c77fdb3d9e"
              }
            }
        }
        stage("Quality Gate") {
            steps {
              timeout(time: 10, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
        }

    }
}