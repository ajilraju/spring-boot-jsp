pipeline {
    agent any

    tools {
        maven '3.8.5'
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
        stage('Deploying Artifcats') {
            steps {
                script {
                    env.APP_VERSION = sh(
                        script: '''
                            perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml
                        ''',
                        returnStdout: true
                    )
                    sh '''
                    ssh deployer@3.142.145.171 "sudo ~/deploy.sh ${APP_VERSION}"
                    '''
                }
            }
        }
    }
}
