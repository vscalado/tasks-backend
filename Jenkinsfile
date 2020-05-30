pipeline{
    agent any
    stages{
        stage('Build Backend') {
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests') {
            steps{
                bat 'mvn test'
            }
        }
        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL') {
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=e136b8155ea0b6dfcc65e0d7e4f94b01f81d6140 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                sleep(10)
                timeout(time: 1, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: true
                }
           }
        }
        stage('Deploy Backend') {
            steps{
                deploy adapters: [tomcat8(credentialsId: 'loginTomCat', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Tests') {
            steps{
                dir('api-test') {
                    git credentialsId: 'loginGithub', url: 'https://github.com/vscalado/tasks-api-rest'
                    bat 'mvn test'
                }
            }
        }
    }
}