pipeline {
    agent any 
    tools {
        maven 'maven'
    }
    envirnment {
        IMAGE_NAME = "springbootapp"
        IMAGE_TAG = "latest"
        ACR_NAME = "springbootcontainerregistry"
        ACR_LOGIN_SERVER = "springbootcontainerregistry.azurecr.io"
        FULL_IMAGE_NAME = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        TENANT_ID = "45a30f73-9233-413b-be36-ad7952e59daa"

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
                    -Dsonar.projectKey=parthibansrinivas_bootcampproject1 \
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
            timeout(time: 1, unit: 'MINUTES'){
                waitForQualityGate abortPipeline: true, credentialsId: 'sonar'
            }
         }
        }
        stage('Docker Build'){
            steps {
                script {
                    echo "Docker Image Build"
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        stage ('Azure Login to ACR'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'azure-acr-sp', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]){
                    script {
                        echo "Login to Azure"
                        sh '''
                        az login --service principal -u AZURE_USERNAME -p AZURE_PASSWORD --tenant $TENANT_ID
                        az acr login --name $ACR_NAME
                        '''
                    }
                }
            }
        }
        stage ('Docker Push to ACR') {
            steps {
                script {
                    echo 'Docker Image Push'
                    sh '''
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} $FULL_IMAGE_NAME
                    docker push ${FULL_IMAGE_NAME}
                    '''
                }
            }
        }
    }
}