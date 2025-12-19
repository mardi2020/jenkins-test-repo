pipeline {
  agent any

  environment {
    IMAGE = "aips-apps.kr.ncr.ntruss.com/test-app:latest"
  }

  stages {
    stage('Pull & Run & Test') {
      steps {
        sh '''
          set -eux

          docker ps
          docker pull ${IMAGE}

          docker rm -f test-app-under-test || true
          docker run -d --name test-app-under-test -p 15000:5000 ${IMAGE}

          # wait a bit
          sleep 2

          STATUS=$(curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:15000/health)
          if [ "$STATUS" != "200" ]; then
            echo "health check failed: status=$STATUS"
            docker logs test-app-under-test || true
            exit 1
          fi
          echo "health check passed (200)"
        '''
      }
    }
  }

  post {
    always {
      sh '''
        docker logs test-app-under-test || true
        docker rm -f test-app-under-test || true
      '''
    }
  }
}
