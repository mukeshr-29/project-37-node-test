pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'npm17'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages{
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/mukeshr-29/project-37-node-test.git'
            }
        }
        stage('file scanning'){
            steps{
                sh 'trivy fs --format table -o trivyfs-report.html .'
            }
        }
        stage('static code analysis'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonnar-token', installationName: 'sonar-server'){
                        sh '''
                        $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=project-35 \
                            -Dsonar.projectKey=project-35
                        '''
                    }
                }
            }
        }
        stage('quality gate'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonnar-token'
                }
            }
        }
        stage('docker build and tag'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        sh '''
                            docker build -t mukeshr29/nodeproject:$BUILD_NUMBER .
                        '''
                    }
                }
            }
        }
        stage('docker img scan'){
            steps{
                sh 'trivy image --format table -o trivyimg.html mukeshr29/nodeproject:$BUILD_NUMBER'
            }
        }
        stage('docker img push'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        sh 'docker push mukeshr29/nodeproject:$BUILD_NUMBER'
                    }
                }
            }
        }
    }
}