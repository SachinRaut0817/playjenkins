pipeline {
  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=671438781287.dkr.ecr.ap-southeast-1.amazonaws.com/kaniko-test:v0.3
            '''
          }
        }
      }
    } 
  }
}
