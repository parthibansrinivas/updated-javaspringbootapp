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
    }
}