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
                git branch: 'main', changelog: false, credentialsId: 'github', poll: false, url: 'https://github.com/ajilraju/spring-boot-jsp.git'
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
        stage('Publishing Artifcats') {
            steps {
                withAWS(profile:'default') {
                    script {
                        env.APP_VERSION = sh(
                            script: '''
                                perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml
                            ''',
                            returnStdout: true
                        )
                        s3Upload(file:"target/news-${APP_VERSION.trim()}.jar", bucket:'keyshell-artifactory', path:'spring-news-app/')
                    }
                    
                }
            }
        }
    }
}
