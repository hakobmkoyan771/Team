pipeline {
  agent any
  triggers {
    GenericTrigger(causeString: 'GenericCause', genericVariables: [[key: 'RELEASE_TAG', value: '$.release.tag_name'],
                                                                   [key: 'REPO_LINK', value: '$.repository.clone_url']]) 
  }
  environment {
    test = "hello"
    TAG_NAME = ""
    COMMIT_SHA = ""
    DEV_REPO = ""
  }
  stages {
    stage("Store Dev Repo Commit Hash & Specify Developer's repo as DEV_REPO") {
      steps {
        script {
          TAG_NAME = "${RELEASE_TAG}"
          DEV_REPO = "${REPO_LINK}"
        }
      }
    }
    stage('Checkout "Tasks" Repo')  {
      steps {
        script {
          try {
            dir('TasksRepo') {
              git branch: 'main', url: "${REPO_LINK}"
              COMMIT_SHA = sh returnStdout: true, script: "git rev-list -n 1 ${RELEASE_TAG}"
              echo COMMIT_SHA
            }
          }
          catch(Exception err) {
            error("Unable to clone developer's repo")
          }
        }  
      }
    }
    stage("Build & Run Testing Container") { 
      steps {
        sh "docker build -t temp_test_img ."
        sh """docker run --rm --name temp_container temp_test_img > answers.txt""" 
      }  
    }
  }
  post {
    success {
      sh "export TAG_NAME=abc"
      sh 'echo $TAG_NAME'
    }
  }
}
/*pipeline {
  agent any
  triggers {
    GenericTrigger(causeString: 'Generic Cause', 
                   genericVariables: [[key: 'COMMIT_HASH', value: '$.head_commit.id'],
                                      [key: 'NAME', value: '$.repository.name']])
  } 
  stages {
    stage("Store commit hash") { 
      steps {
        script {
          try {
            sh """docker exec redis_server redis-cli "set" "BEFORE_TEST" "${COMMIT_HASH}" """
            sh """docker exec redis_server redis-cli "save" """
          }
          catch(Exception err) {
            error("Unable to store commit hash")
          }
        }
      }
    }
    stage('Checkout "Tasks" Repo')  {
      steps {
        script {
          try {
            dir('TasksRepo') {
              git branch: 'main', url: "https://github.com/hakobmkoyan771/PythonWorkdir.git"
            }
          }
          catch(Exception err) {
            error("Unable to clone developer's repo")
          }
        }  
      }
    }
    stage("Build & Run Testing Container") { 
      steps {
        sh "docker build -t temp_test_img ."
        sh """docker run --rm --name temp_container temp_test_img > answers.txt""" 
      }  
    }
  }
}*/
