pipeline {
  agent any

  environment {
    IMAGE_NAME = "eduardvalentin/plone-backend"
    GIT_NAME = "plone-backend"
  }

  parameters {
    string(defaultValue: '', description: 'Run tests with GIT_BRANCH env enabled', name: 'TARGET_BRANCH')
  }
  
  stages {
    // stage('Build & Test') {
    //   environment {
    //     TAG = BUILD_TAG.toLowerCase()
    //   }
    //   steps {
    //       script {
    //         try {
    //           checkout scm
    //           sh '''docker -v'''
    //           sh '''hostname'''
    //           // sh '''docker ps -a | grep dind'''
    //           sh '''docker build --no-cache -t ${IMAGE_NAME}:${TAG} .'''
    //           sh '''./test/run.sh ${IMAGE_NAME}:${TAG}'''
    //         } finally {
    //           sh script: "docker rmi ${IMAGE_NAME}:${TAG}", returnStatus: true
    //         }
    //       }
    //   }
    // }
 
    stage('Release on tag creation') {
      when {
        buildingTag()
      }
      steps{
          echo "This is a tag build: ${env.GIT_TAG}"
          withCredentials([string(credentialsId: 'eddie-eea-jenkins-token', variable: 'GITHUB_TOKEN'),  usernamePassword(credentialsId: 'eddie-jekinsdockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
           sh '''docker pull eeacms/gitflow; docker run -i --rm --name="$BUILD_TAG"  -e GIT_BRANCH="$BRANCH_NAME" -e GIT_NAME="$GIT_NAME" -e DOCKERHUB_REPO="eduardvalentin/plone-backend" -e GIT_TOKEN="$GITHUB_TOKEN" -e DOCKERHUB_USER="$DOCKERHUB_USER" -e DOCKERHUB_PASS="$DOCKERHUB_PASS" -e DEPENDENT_DOCKERFILE_URL="eea/eea-website-backend/blob/master/Dockerfile eea/fise-backend/blob/master/Dockerfile eea/advisory-board-backend/blob/master/Dockerfile eea/bise-backend/blob/plone-6/Dockerfile" -e GITFLOW_BEHAVIOR="RUN_ON_TAG" eeacms/gitflow'''
         }

      }
    }


 }

  post { 
    always {
      cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
    }
    changed {
      script {
        def details = """<h1>${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${currentBuild.currentResult}</h1>
                         <p>Check console output at <a href="${env.BUILD_URL}/display/redirect">${env.JOB_BASE_NAME} - #${env.BUILD_NUMBER}</a></p>
                      """
        emailext(
        subject: '$DEFAULT_SUBJECT',
        body: details,
        attachLog: true,
        compressLog: true,
        recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'CulpritsRecipientProvider']]
        )
      }
    }
  }
}
