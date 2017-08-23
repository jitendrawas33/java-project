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
        sh "rm -rf /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
     }
      stage('Running on CentOS') {
      agent {
       label 'centOS'
            }
       steps {
       sh "wget http://jsbourne1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
       sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 5 4"
       }
      }
      stage("Test on Debian") {
      agent {
       docker 'openjdk:8u121-jre'
            }
       steps {
      sh "wget http://jsbourne1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
      sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
             }
       }
      stage('promote to green')  {
      agent {
      label 'Apache'
    }
     when {
         branch 'master'
     }
       steps {
        sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
            }
}
       stage('promote Development Branch to Master') {
        agent {
          label 'Apache'
           }
        when {
          branch 'development'
          }
        steps {
         echo "Stashing Any local Changes"
         sh 'git stash'
         echo "Checking Out Development Branch"
         sh 'git checkout development'
         echo "Checking Out Master Branch"
         sh 'git checkout master'
         echo "Merging Development into Master Branch"
         sh 'git merge development'
         echo "Pushing to origin master"
         sh 'git push orgin master'
      }
     }
 }
}
