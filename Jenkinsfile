pipeline {
  agent any
  stages {
    stage('Clone') {
      steps {
        git(url: 'https://github.com/tusharbiswas9038/devops-demo.git', branch: 'main')
      }
    }

    stage('Build') {
      steps {
        sh 'docker compose build'
      }
    }

    stage('Test API') {
      steps {
        sh 'docker compose up -d'
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
