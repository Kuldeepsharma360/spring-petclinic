pipeline {
  agent any
  stages {
    stage('compile') {
      steps {
        sh './mvnw clean compile'
      }
    }

    stage('Static Analysis') {
      steps {
        sh '''./mvnw sonar:sonar \\
  -Dsonar.projectKey=petclinic \\
  -Dsonar.projectName=\'petclinic\' \\
  -Dsonar.host.url=http://3.110.138.66:9000 \\
  -Dsonar.token=sqp_3d612a00bd89e5975153a68ff3a80b83c940974f'''
      }
    }

    stage('unit test') {
      steps {
        sh './mvnw "-Dtest=**/petclinic/*/*.java" test'
      }
    }

    stage('package') {
      steps {
        sh './mvnw package -DskipTests=true'
      }
    }

    stage('deploy') {
      parallel {
        stage('deploy') {
          agent {
            node {
              label 'test'
            }

          }
          steps {
            sh './mvnw spring-boot:run </dev/null &>/dev/null &'
          }
        }

        stage('integration and performance test') {
          steps {
            sh './mvnw verify -DskipTests=true'
            junit 'target/surefire-reports/TEST-*.xml'
            perfReport 'target/jmeter/results/*'
          }
        }

      }
    }

  }
}