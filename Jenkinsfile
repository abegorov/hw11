pipeline {
    agent {
        node {
            label 'docker-agent'
        }
    }
    parameters {
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'Docker image version')
    }
    stages {
        stage('Build war') {
            agent {
                docker {
                    image 'maven:3-eclipse-temurin-17-alpine'
                    reuseNode true
                }
            }
            steps {
                dir('puzzle15') {
                    git 'https://github.com/venkaDaria/puzzle15'
                    dir('target') { deleteDir() }
                    dir('target_heroku') { deleteDir() }
                    sh 'find src -type f -name "*.java" -exec sed -i "s/javax\\./jakarta\\./g" {} \\;'
                    sh 'find src -type f -name "*.jsp" -exec sed -i "s@http://java.sun.com/jsp/jstl/core@jakarta.tags.core@g" {} \\;'
                    sh 'cp ../pom.xml ./'
                    sh 'mvn clean'
                    sh 'mvn package'
                }
            }
        }
        stage('Build and push docker image') {
            agent {
                docker {
                    image 'docker:cli'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                    reuseNode true
                }
            }
            steps {
                sh 'echo dummy'
            }
        }
        stage('Push docker image') {
            agent {
                docker {
                    image 'docker:cli'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                    reuseNode true
                }
            }
            steps {
                sh 'echo dummy'
            }
        }
        stage('Deploy') {
            matrix {
                axes {
                    axis {
                        name 'SERVER'
                        values 'yandex3'
                    }
                }
                stages {
                    stage('Deploy on ${SERVER}') {
                        agent {
                            node {
                                label "${SERVER}"
                            }
                        }
                        steps {
                            sh "echo dummy ${SERVER}"
                        }
                    }
                }
            }
        }
    }
}
