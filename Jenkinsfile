pipeline {
  agent any

  stages {
    stage('Install Apache2') {
      steps {
        sh '''
          sudo apt-get update -y
          apt-get install -y apache2
          sudo systemctl enable apache2
        '''
      }
    }

    stage('Generate Test Requests') {
      steps {
        sh '''
          curl -s -o /dev/null -w "%{http_code}\\n" http://localhost/not_existing_file.html || true
        '''
      }
    }

    stage('Check Logs for Errors') {
      steps {
        sh '''
          LOG_FILE="/var/log/apache2/access.log"
          sudo touch "$LOG_FILE"
          if sudo grep -E "HTTP/[0-9.]+\" [45][0-9]{2}" "$LOG_FILE" >/dev/null; then
            echo "Found 4xx/5xx errors in $LOG_FILE"
            sudo grep -E "HTTP/[0-9.]+\" [45][0-9]{2}" "$LOG_FILE" || true
            exit 1
          else
            echo "No 4xx/5xx errors found"
          fi
        '''
      }
    }
  }
  }
}
