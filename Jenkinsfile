pipeline {
    agent any
    environment {     
    DOCKERHUB_CREDENTIALS= credentials('dockerhub')     
    } 
    stages{
        stage('git checkout') {
            steps{
              git branch: 'master', url: 'https://github.com/chowdarybvsn/mrdevops-cicd.git'
            }
        }
        stage('build') {
            steps{
               sh 'mvn clean install'
            }
        }
        stage('static code analysis') {
            steps{
                script{
                  withSonarQubeEnv(credentialsId: 'mysonar') {
                   sh 'mvn sonar:sonar'
                  }
                }
            }
        }    
        stage('QUALITY GATES') {
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'mysonar'
                  }
            }
        }
        stage('docker build') {
            steps{
                sh 'docker image build . -t bootcamp'
            }
        }
        stage('docker tag') {
            steps{
                sh 'docker image tag bootcamp chowdarybvsn/bootcamp'
                sh 'docker image tag bootcamp chowdarybvsn/bootcamp:latest'
            }
        }
        stage('docker hub login') {
            steps{
                script{
                   sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                }
            }  
        }
        stage('docker image push to dockerhub') {
            steps{
                   sh 'docker image push chowdarybvsn/bootcamp:latest'
                } 
            }
    }
}
