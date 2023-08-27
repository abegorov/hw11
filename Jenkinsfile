pipeline {
    agent {
        node {
            label 'docker-agent'
        }
    }
    parameters {
        string(name: 'VERSION', defaultValue: '1.0.7', description: 'Docker image version')
    }
    stages {
        stage('Build war') {
            agent {
                docker {
                    image 'maven:3-eclipse-temurin-17-alpine'
                    args '-v maven-repo:/root/.m2'
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
                sh 'cp puzzle15/target/Puzzle15-1.0-SNAPSHOT.war tomcat-puzzle15/puzzle15.war'
            }
        }
        stage('Build and Push docker image') {
            agent {
                docker {
                    image 'docker:cli'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                    reuseNode true
                }
            }
            steps {
                dir('tomcat-puzzle15') {
                    script {
                        def image = docker.build("abegorov/tomcat-puzzle15:$VERSION")
                        docker.withRegistry('', 'dockerhub_credentials') {
                            image.push()
                        }
                    }
                }
            }
        }
        stage('Deploy Matrix') {
            input {
                message "Confirm deploy"
                ok "Go!"
            }
            matrix {
                axes {
                    axis {
                        name 'SERVER'
                        values 'yandex2', 'yandex3'
                    }
                }
                stages {
                    stage('Deploy') {
                        agent {
                            node {
                                label "${SERVER}"
                            }
                        }
                        steps {
                            sh "docker rm --force puzzle15 || true"
                            script {
                                def image = docker.image("abegorov/tomcat-puzzle15:$VERSION")
                                docker.withRegistry('', 'dockerhub_credentials') {
                                    image.run(
                                        ' --name puzzle15' +
                                        ' --restart=unless-stopped' +
                                        ' --detach' +
                                        ' --publish 80:8080 '
                                    )
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
