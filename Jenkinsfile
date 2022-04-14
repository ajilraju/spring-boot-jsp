pipeline {
    agent any

    tools {
        maven '3.8.5'
    }
    
    parameters {
        string(name: 'SERVER_IP', defaultValue: '127.0.0.1', description: 'Provide production server IP Address.')
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'develop', changelog: false, credentialsId: 'github', poll: false, url: 'https://github.com/ajilraju/spring-boot-jsp.git'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Copying Artifcats') {
            steps {
                sh '''
                    version=$(perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml)
                    rsync -avzP target/news-${version}.jar root@${SERVER_IP}:/opt/
                '''
            }
        }
    }
}
