pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  securityContext:
    runAsNonRoot: true
    seccompProfile:
      type: RuntimeDefault
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    securityContext:
      runAsNonRoot: true
      runAsUser: 1000
      allowPrivilegeEscalation: false
      capabilities:
        drop: ["ALL"]
      seccompProfile:
        type: RuntimeDefault
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: regcred
        items:
          - key: .dockerconfigjson
            path: config.json
"""
        }
    }

    stages {
        stage('Fix Permissions') {
            steps {
                script {
                    sh "sleep 10"  // ⏳ Adding sleep to ensure permissions take effect
                }
            }
        }

        stage('Kaniko Build & Push Image') {
            steps {
                container('kaniko') {
                    sh '''
                    echo "Waiting for 5 seconds before starting Kaniko..."
                    sleep 100  # ⏳ Adding sleep before running Kaniko

                    /kaniko/executor --dockerfile /home/jenkins/agent/workspace/kaniko/Dockerfile \
                    --context /home/jenkins/agent/workspace/kaniko \
                    --destination=671438781287.dkr.ecr.ap-southeast-1.amazonaws.com/kaniko-test:v0.2
                    '''
                }
            }
        }
    }
}
