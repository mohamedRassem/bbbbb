pipeline {
    agent any
environment {
    		DOCKERHUB_CREDENTIALS=credentials('projet')
    	}

    stages {
        stage('Récupération du code de la branche') {
            steps {
                git branch: 'master',
                url: 'https://https://github.com/mohamedRassem/5NIDS2-G10-projet2.git'
            }
        }

        stage('Nettoyage et compilation avec Maven') {
            steps {
                // Étape de nettoyage du projet
                sh "mvn clean"

                // Étape de compilation du projet
                sh "mvn compile"
                // Etape de sonar
            }
        }

        stage('MVN SONARQUBE'){
        steps {
            sh "mvn sonar:sonar -Dsonar.login=sqp_b91570717216200722d766f7048e2748bd405e42"
        }
        }
        stage("mockito"){
            steps {
                sh 'mvn -Dtest=KaddemApplicationTests test'
            }
        }
        stage('mvn deploy'){
            steps {
                sh "mvn deploy"
            }
        }

        stage('Docker Image') {
                           steps {
                               sh 'docker build -t mahdiennour-5nids2-g3 .'
                           }
               }
stage('DOCKERHUB') {
                          steps {
                              sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                              sh 'docker tag mahdiennour-5nids2-g3 mahdienn/mahdienn-5nids2-g3:1.0.0'
                              sh 'docker push mahdienn/mahdienn-5nids2-g3:1.0.0'
                          }
                      }
               stage('Docker Compose') {
                                  steps {
                                      sh 'docker compose up -d'
                                  }
                      }

    }
}
