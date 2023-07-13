pipeline {
  agent any
  environment {
  Java_Home = tool name: 'java-20', type: 'jdk'
  }
  stages {
      stage('Snyk Test using plugin') {
            // Run snyk test to check for vulnerabilities and fail the build if any are found
            steps {
                // Run snyk test, output results as json and then run snyk-filter using that json and the location of the filter.
                snykSecurity( 
                    snykInstallation: 'snyk@latest', 
                    snykTokenId: 'snyk_api_token', 
                    monitorProjectOnBuild: false, // snyk-filter is not supported with monitor, so this should be set to false.
                    failOnIssues: 'false', // if the build fails in the snykSecurity step, snyk-filter will not run, which is why failOnIssues is set to false.
                    additionalArguments: '--json-file-output=all-vulnerabilities.json'
                )
            }
      }
      stage('Build Artifact') {
            steps {
              withMaven(maven: 'maven') {
              sh "mvn clean package -DskipTests=true" 
              archive 'target/*.jar'
              }
            }  
       }
      stage('Test Maven - JUnit') {
            steps {
              withMaven(maven: 'maven') {
              sh "mvn test"
              }
            }
            post{
              always{
                junit 'target/surefire-reports/*.xml'
              }
            }
        }
        

       stage('Sonarqube Analysis - SAST') {
            steps {
              withMaven(maven: 'maven') {
              sh "mvn clean verify sonar:sonar \
                -Dsonar.token=d9f2df603842a18921cac69fa3705a3cbc257126 \
                -Dsonar.projectKey=karthik0741_Part2_Petclinic \
                -Dsonar.organization=karthik0741 \
                -Dsonar.host.url=https://sonarcloud.io" 
                          }     
              }
        }
        stage('Docker Image creation') {
            steps {
              withDockerRegistry(credentialsId: 'dockercred', url: 'https://hub.docker.com/') {
              sh "docker build -t petclinic_img ."
              sh "docker push petclinic_img"
              }
            }  
       }
     }
}
