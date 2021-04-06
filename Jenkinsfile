pipeline {
  agent any
  stages {
    stage('clone down') {
     agent {
       label 'swarm'
     }
     steps {
       stash excludes: '.git', name: 'code'
     }
    }
    stage('Parallel execution') {
      parallel {
        stage('Say hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }
          options { skipDefaultCheckout(true) }
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

  }
}
