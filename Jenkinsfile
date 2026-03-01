pipeline {
  agent any
  tools {
    maven 'Maven_3_9_6'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'Sonarqubee', variable: 'Sonarqubee')]) {
          bat'''
          mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar ^
            -Dsonar.projectKey=Mavenn ^
            -Dsonar.projectName=Mavenn ^
            -Dsonar.host.url=http://localhost:9000 ^
            -Dsonar.token=%Sonarqubee%
          '''
        }
      }
    }
    stage('Build') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: "https://index.docker.io/v1/"]) {
          script {
            app = docker.build("goutham2135/testeb")
          }
        }
      }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              bat'''
              "D:\\Devsecops\\Container scan demo\\snyk-win.exe" container test goutham2135/testeb
              '''
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          bat("mvn snyk:test -fn")
        }
      }
    }
    stage('RunDASTUsingZAP') {
      steps {
        bat'''
        "C:\\Program Files\\ZAP\\Zed Attack Proxy\\zap.sh" ^
          -port 9393 -cmd -quickurl https://www.example.com ^
          -quickprogress ^
          -quickout "C:\\Program Files\\ZAP\\Zed Attack Proxy\\Output.html"
        '''
      }
    }

    stage('checkov') {
      steps {
        bat'''
        checkov -s -f main.tf
        '''
      }
    }

  }
}
