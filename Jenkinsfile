pipeline {
    agent any
    stages{
        stage('Code Checkout') {
            steps {
                git branch: 'Development', url: 'https://github.com/ChanakyaShivaTeja/Deployment_Assignment'
            }
        }
        stage('Sonar Report') {
            steps {
                withSonarQubeEnv(installationName: 'deployment-sonarQube') {
                    sh 'mvn clean verify sonar:sonar'
                    sleep 60;
                }
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("Build") {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Send Artifacts via SSH server') {
            steps([$class: 'BapSshPromotionPublisherPlugin']) {
                sshPublisher(
                    continueOnError: false, failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName:"deployment-ssh-server",
                            verbose: true,
                            transfers:[
                                sshTransfer(
                                    sourceFiles:"**/*.jar",
                                    remoteDirectory:"",
                                    execCommand:"nohup java -jar target/*.jar &"
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }
}