pipeline{
    agent none

    environment {
      MAJOR_VERSION = 1
    }

    stages{

      stage('Unit Tests'){
        agent {
          label 'apache'
        }
        steps{
          sh 'ant -f test.xml -v'
          junit 'reports/result.xml'
        }
      }
      stage('build'){
        agent {
          label 'apache'
        }
        steps{
          sh 'ant -f build.xml -v'
        }
        post{
          success{ 
              archiveArtifacts artifacts: 'dist/*jar', fingerprint: true
              }
          }
      }

      stage('deploy'){
        agent {
          label 'apache'
        }
        steps{
          sh "mkdir -f /var/www/html/rectangles/all/${env.BRANCH_NAME}"
          sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
        }
      }

      stage('Promote to green'){
        agent {
          label 'apache'
        }
        when {
          branch 'master'
        }
        steps{
          sh "cp /var/www/html/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        }
      }

      stage('Promote dev to master'){
         agent {
          label 'apache'
        }
         when {
          branch 'development'
        }

        steps{
            echo "Stashing any local changes"
            sh 'git stash'
            echo "Checkout dev"
            sh 'git checkout development'
            echo "checkout master"
            sh 'git checkout master'
            echo "Merge dev to master"
            sh 'git merge development'
            echo "pushing to master"
            sh 'git push origin master'
            echo "tagging"
            sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
            sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        }
      }
    }
}