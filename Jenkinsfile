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

        stage('DAST'){
            steps{
        		
        		script{
        		   env.DOCKER = tool "Docker"
        		   env.DOCKER_EXEC = "${DOCKER}/bin/docker"

        	
        		    sh '${DOCKER_EXEC} pull owasp/zap2docker-stable'
                    sh '${DOCKER_EXEC} rm -f zap2'
                    sh '${DOCKER_EXEC} run --add-host="localhost:0.0.0.0" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8090:8090 -d owasp/zap2docker-stable zap.sh -daemon -port 8090 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:0.0.0.0" -v $(pwd):/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-baseline.py -t "http://zero.webappsecurity.com" -I -r zap_baseline_report.html -l PASS'	
        		   
        		   publishHTML(target: [
        				    allowMissing: false,
        				    alwaysLinkToLastBuild: true,
        				    keepAll: true,
        				    reportDir: 'reports',
        				    reportFiles: 'zap_baseline_report.html',
        				    reportName: 'HTML Report',
        				    reportTitles: 'The Report'])
        		}
            }
        }
        
        
    }
    
           
}
