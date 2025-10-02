pipeline {
    agent any 
    tools {
        maven 'maven'
    }
    stages {
        stage('checkout from Git') { 
            steps {
                git branch: 'main', url: 'https://github.com/parthibansrinivas/updated-javaspringbootapp.git'
            }
        }
        stage('Maven Validate'){
            steps {
                sh 'mvn validate'
            }
        }
        stage('Maven Compile'){
            steps {
                sh 'mvn compile'
            }
        }
        stage('Sonar Analysis'){
            environment {
                SCANNER_HOME = tool 'Sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarserver'){
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.organization=parthibansrinivas \
                    -Dsonar.projectName=BootcampProject1 \
                    -Dsonar.projectkey=parthibansrinivas_bootcampproject1 \
                    -Dsonar.java.binaries=.
                    '''
                }
            }
        }
        stage('Maven Package'){
            steps {
                sh 'mvn package'
            }
        }
        stage('Sonar Quality Gate'){
         steps {
            timeout(timeout: 1, unit: 'MINUTES'){
                waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
            }
         }
        }
    }
}