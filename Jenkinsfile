pipeline {
  agent any
  
  stages {
      stage('Build Artifact') {
            steps {
              env.JAVA_HOME="${tool 'java'}"
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
                
              sleep(60)
              timeout(time: 2, unit: 'MINUTES') {
                      script {
                        waitForQualityGate abortPipeline: true
                      }
                }
              }
        }
     }
}
