pipeline {
  agent any
  tools {
    maven 'Maven_3_9_6'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'sqp_ec899f6777977b7467c376643c3c1c29664355bc', variable: 'sqp_ec899f6777977b7467c376643c3c1c29664355bc')]) {
          bat("mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=$sqp_ec899f6777977b7467c376643c3c1c29664355bc -Dsonar.projectKey=Goutham2135Java -Dsonar.host.url=http://localhost:9000/")
        }
      }
    }
    stage('Build') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("asecurityguru/testeb")
          }
        }
      }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              bat("C:\\snyk\\snyk-win.exe  container test asecurityguru/testeb")
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
        bat("C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\Output.html")
      }
    }

    stage('checkov') {
      steps {
        bat("checkov -s -f main.tf")
      }
    }

  }
}
