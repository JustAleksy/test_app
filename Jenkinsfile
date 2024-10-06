pipeline {
    agent any

    environment {
        // Переменные окружения
        DOCKER_HUB_CREDENTIALS = '0fb1329c-e01b-46f0-80b3-640aca04b2a8' ID учетных данных Docker Hub в Jenkins
        DOCKER_IMAGE = "justmeat/test-app:${env.BUILD_NUMBER}"
        KUBE_CONFIG = credentials('b22ac297-e610-4102-836e-fc26d67c5485') // ID kubeconfig файла в Jenkins Credentials
    }

    stages {
        stage('Checkout') {
            steps {
                // Клонируем репозиторий
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Логинимся в Docker Hub и собираем образ
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIALS) {
                        def app = docker.build("${DOCKER_IMAGE}")
                        app.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    // Обновляем образ в Kubernetes Deployment
                    sh """
                    kubectl set image deployment/your-deployment-name your-container-name=${DOCKER_IMAGE}
                    """
                }
            }
        }
    }

    post {
        always {
            // Очищаем Docker-окружение
            cleanWs()
        }
        failure {
            // Отправляем уведомление при сбое
            mail to: 'imformeat@gmail.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
