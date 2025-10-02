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
        
    }
}