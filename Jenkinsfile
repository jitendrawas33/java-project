pipeline {
     agent none

  stages {
      stage('Unit Tests') {
       agent {
      label 'Apache'
    }
       steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
       } 
      }
      stage('build') {
       agent {
      label 'Apache'
    }
      steps {
      sh 'ant -f build.xml -v'
      }
      post {
   success {
    archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
   }
  }

      }
      stage('deploy') {
      agent {
      label 'Apache'
    }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
     }
      stage('Running on CentOS') {
      agent {
       label 'centOS'
            }
       steps {
       sh "wget http://jsbourne1.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
       sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 5 4"
       }
      }
      stage("Test on Debian") {
      agent {
       docker 'openjdk:8u121-jre'
            }
       steps {
      sh "wget http://jsbourne1.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
      sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
             }
       }
  }
 }

