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
            environment{
                scannerHome = tool 'SONAR_LOCAL'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL_QG'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=e136b8155ea0b6dfcc65e0d7e4f94b01f81d6140 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
    }
}


-Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=e136b8155ea0b6dfcc65e0d7e4f94b01f81d6140 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java 