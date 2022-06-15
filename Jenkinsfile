pipeline {
    agent any

    tools {
        maven 'mvn-3.8.5'
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'batch-3', changelog: false, credentialsId: 'github-user-pass', poll: false, url: 'https://github.com/ajilraju/spring-boot-jsp.git'
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
            environment {
                PUB_KEY = credentials('web-srv-pub')
            }
            steps {
                sh '''
                    printenv
                    version=$(perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml)
                    rsync -avzPe target/news-${version}.jar demo@3.87.207.128:~/
                '''
            }
        }
    }
}
