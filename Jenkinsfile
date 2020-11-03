pipeline {
  agent none
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'test maven app'
        sh 'mvn clean test'
      }
    }

    stage('package') {
      parallel {
        stage('package') {
          agent {
            docker {
              image 'maven:3.6.3-jdk-11-slim'
            }

          }
          steps {
            echo 'package maven app'
            sh 'mvn package -DskipTests'
            archiveArtifacts 'target/*.war'
          }
        }

        stage('Sonarqube') {
          environment {
            SONAR_TOKEN = '871038e3ea5c38741b9900ec838d7ed6d719178b'
          }
          steps {
            sh '''mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar
'''
          }
        }

      }
    }

    stage('Docker Build and Publish') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {

            def dockerImage = docker.build("initcron/sysfoo:v${env.BUILD_ID}", "./")

            dockerImage.push()

            dockerImage.push("latest")

          }
        }

      }
    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
}