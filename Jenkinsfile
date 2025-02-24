pipeline {
  agent any
    environment {
      docker_username="uilleficode"
    }
  stages {
    stage('Clone down') {
      steps {
        stash(excludes: '.git', name: 'code')
      }
    }

    stage('Parallel execution') {
      parallel {
        stage('Say Hello if master') {
          when {branch "master"}
          steps {
            sh 'echo "On master branch"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout()
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            stash 'code'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

    stage('push docker app') {
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
      }
    }

    stage('component tests') {
      when {
        anyOf {
          branch "master"
          changeRequest()
        }
      }
      steps {
        sh 'echo "This is where the component test would be"'
      }
    }

  }
}