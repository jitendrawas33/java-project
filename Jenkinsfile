pipeline {
     agent {master}

  
  stages {
     stage('PRINT') {
      steps {
      echo " Job Name is : $JOB_NAME"
      }
      }
     stage('WRITE') {
      steps {
      echo "$BUILD_NUMBER" >> build_number
      }
      }
     stage('READ') {
      steps {
      sh 'cat build_number'
      }
      }
    }
  post {
   always {
    archiveArtifacts artifacts: 'dist/build_number', fingerprint: true
   }
  }
 }

