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
    }
}