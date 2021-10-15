pipeline {
    agent any

    tools {
        maven 'Maven'
    }
  
    stages {
        stage('initial'){
            steps{
             sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              echo java -version
              '''
            }
        }
        
        stage('Compile'){
            steps{
                sh 'mvn -B package --file pom.xml'
            }
        }
        
        stage('Sonarqube'){
            steps{
                script{
                    def scannerHome = tool 'SonarQube Scanner'
                    
                    withSonarQubeEnv('Sonar Server'){
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ms-maven -Dsonar.sources=. -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
                    }
                }
            }
        }
        
    }
    
           
}
