pipeline {
  agent any
  options { timestamps(); disableConcurrentBuilds() }
  parameters { string(name:'APP_HOST', defaultValue:'3.25.125.216', description:'Application EC2 public IP or DNS') }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Backend Test') { steps { dir('backend') { sh 'npm install'; sh 'npm test' } } }
    stage('Frontend Build') { steps { dir('frontend') { sh 'npm install'; sh 'npm run build' } } }
    stage('Docker Build Validation') { steps { sh 'docker build -t fullstack-backend:ci backend'; sh 'docker build -t fullstack-frontend:ci frontend' } }
    stage('Deploy to Application EC2') {
      steps {
        sshagent(credentials:['app-ec2-key']) {
          sh '''
            set -eu
            case "$APP_HOST" in
              ""|3.25.125.216); exit 1 ;;
            esac
            ssh -o StrictHostKeyChecking=accept-new ubuntu@"$APP_HOST" 'if [ -d /home/ubuntu/fullstack-cicd-project/.git ]; then cd /home/ubuntu/fullstack-cicd-project && git pull --ff-only origin main; else git clone https://github.com/laveenakash/AWS-EC2_Instances-01.git /home/ubuntu/fullstack-cicd-project; fi && cd /home/ubuntu/fullstack-cicd-project && sudo docker compose up -d --build --remove-orphans && curl -fsS http://localhost/api/health'
          '''
        }
      }
    }
  }
  post { success { echo 'CI/CD pipeline completed successfully.' } failure { echo 'Pipeline failed. Review Console Output.' } always { echo 'Pipeline execution finished.' } }
}
