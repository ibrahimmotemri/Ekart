pipeline {
    agent any
    tools{
        jdk     'jdk17'
        maven   'maven3'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '481ed833-d6c9-4f3f-b3c1-4e6a223b7508', poll: false, url: 'https://github.com/jaiswaladi246/Ekart'
            }
        }
        stage('COMPILE') {
            steps {
                sh 'mvn clean compile -DskipTests=true'
            }    
        }
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }    
        }
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Ekart \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Ekart '''
                }
                
            }    
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }    
        }
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'd8ea8cc9-d733-431c-acfd-527010348183', toolName: 'docker')
                    {
                        
                        sh "docker build -t ekart -f docker/Dockerfile ."
                        sh "docker tag ekart ibrahimmotemri2/ekart:latest"
                        sh "docker push ibrahimmotemri2/ekart:latest"
                    }    
                    }
            }    
        }
      
    }
  }
