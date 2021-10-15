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
              '''
            }
        }
        
        stage('Compile'){
            steps{
                sh 'mvn -B package --file pom.xml'
            }
        }

        stage('Test'){
            steps{
                sh 'mvn clean test -e'
            }
        }
        
        stage('Sonarqube'){
            steps{
                script{
                    def scannerHome = tool 'SonarQube Scanner'
                    
                    withSonarQubeEnv('SonarQube'){
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=devsecops -Dsonar.sources=. -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
                    }
                }
            }
        }

         stage('SCA'){
            steps{
                sh 'mvn org.owasp:dependency-check-maven:check'
                
                archiveArtifacts artifacts: 'target/dependency-check-report.html', followSymlinks: false
            }
        }
        
        
    }
    
           
}
