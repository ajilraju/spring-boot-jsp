pipeline {
    agent any

    tools {
        maven '3.8.5'
    }

    stages {
        stage('Source') {
            steps {
              git branch: 'main', changelog: false, credentialsId: 'b0ce76ff-5532-4815-8184-9db4739e20a6', poll: false, url: 'https://github.com/rajil786/spring-boot-jsp.git'
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
        stage('Deploy to stage') {
            steps {
                sh '''
                    version=$(perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml)
                  java -jar -Dserver.port=8085 target/news-${version}.jar
                '''
            }
        }
    }
}
