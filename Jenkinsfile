pipeline {
  agent any
  stages {
    stage('Clone') {
      steps {
        git 'https://github.com/<your-username>/<repo-name>.git'
      }
    }

    stage('Build') {
      steps {
        sh 'docker-compose build'
      }
    }

    stage('Test API') {
      steps {
        sh 'docker-compose up -d'
        sh 'curl -f http://localhost:5000/api || exit 1'
      }
    }

    stage('Clean Up') {
      steps {
        sh 'docker-compose down'
      }
    }

  }
}