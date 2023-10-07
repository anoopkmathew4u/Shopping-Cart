pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/anoopkmathew4u/Shopping-Cart.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://54.236.30.52:9000/ -Dsonar.login=squ_b235a1b1b98b536724709aac1e007898e54b6828 -Dsonar.projectName=shopping-cart \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=shopping-cart '''
            }
        }
        stage('OSWAP SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Build_App') {
            steps {
                sh 'mvn clean install -DskipTests=true'
            }
        }
        stage('Build&PUSH Docker image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t shopping:latest -f docker/Dockerfile .'
                        sh 'docker tag shopping:latest anoopkamthew4u/shopping:latest'
                        sh 'docker push anoopkamthew4u/shopping:latest'
}
                }
            }
        }
        stage('CD_Trigger') {
            steps {
                build job: "CD_pipe" , wait: true
            }
        }
    }
}
