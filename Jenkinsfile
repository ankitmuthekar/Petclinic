pipeline {
    agent any
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jaiswaladi246/Petclinic.git'           
            }
        }
         
          stage('Compile') {
            steps {
               sh "mvn clean compile"
               
            }
        }
        
        stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        stage('SonarQube Analysis') {
            steps {
               withSonarQubeEnv('sonar-server') {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
                
               }
            }
        }
         stage('Build') {
            steps {
               sh "mvn clean package"
               
            }
        }
         stage('Build Docker Image') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '8df00c29-a3b3-4b5b-b3a5-8fb5810b6e30', toolName: 'Docker') {
                    sh "docker build -t petclinic -f docker/Dockerfile ."
                    sh "docker tag petclinic ankitmuthekar/petclinic:latest"
                   }
               }
               
            }
        }
         stage('Deploy') {
            steps {
               sh "sudo cp target/petclinic.war /opt/apache-tomcat-9.0.65/webapps"
               
            }
        }
    }
}
