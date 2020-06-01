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
                sleep(20)
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
        stage('Deploy Frontend') {
            steps{
                dir('frontend') {
                    git credentialsId: 'loginGithub', url: 'https://github.com/vscalado/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'loginTomCat', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Functional Tests') {
            steps{
                sleep(5)
                dir('functional-test') {
                    git credentialsId: 'loginGithub', url: 'https://github.com/vscalado/tasks-functional-tests'
                    bat 'mvn test'
                }
            }
        }
        stage('Deploy Prod') {
            steps{
                bat 'docker-compose build'
                bat 'docker-compose up -d'
            }
        }
        stage('Health Check') {
            steps{
                sleep(20)
                dir('functional-test') {
                    bat 'mvn verify -Dskip.surefire.tests'
                }
            }
        }       
    }
    post {
        always{
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*xml, api-test/target/surefire-reports/*.xml,functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/8.xml'
        }
    }
}