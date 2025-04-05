pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'my_sonar'  
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'git-creds', url: 'https://github.com/your-org/your-repo.git', branch: 'main'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(my_sonar) {
                    script {
                        def projectKey = "my-app-${env.BUILD_ID}"
                        def projectName = "My App Build #${env.BUILD_ID}"

                        sh """
                            mvn clean verify sonar:sonar \\
                            -Dsonar.projectKey=${projectKey} \\
                            -Dsonar.projectName='${projectName}' \\
                            -Dsonar.projectVersion=${BUILD_ID} \\
                            -Dsonar.qualitygate.wait=true
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Quality Gate failed: ${qg.status}"
                        } else {
                            echo "Quality Gate passed!"
                        }
                    }
                }
            }
        }
    }
}
