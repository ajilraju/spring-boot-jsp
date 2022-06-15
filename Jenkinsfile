pipeline {
    agent any
    
    triggers { cron('* * * * *') }

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
                    version=$(perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml)
                    rsync -avzPe 'ssh -i $PUB_KEY' target/news-${version}.jar demo@3.87.207.128:~/
                '''
            }
        }
    }
}
