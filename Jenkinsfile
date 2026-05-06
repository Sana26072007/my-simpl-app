pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'Sana26072007/my-simple-app'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }
    stages {
        stage('Проверка кода') {
            steps {
                echo 'Скачиваю код из GitHub...'
                git branch: 'main',
                    url: 'https://github.com/ваш_логин_github/my-simple-app.git'
            }
        }
        stage('Сборка Docker образа') {
            steps {
                script {
                    echo 'Собираю Docker образ...'
                    sh "sed -i 's/BUILD_VERSION/${BUILD_NUMBER}/' index.html"
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }
        stage('Тестирование') {
            steps {
                script {
                    echo 'Проверяю, что контейнер работает...'
                    sh """
                        docker run -d --name test-container ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        sleep 5
                        docker ps | grep test-container
                        docker stop test-container
                        docker rm test-container
                    """
                }
            }
        }
        stage('Деплой на сервер') {
            steps {
                script {
                    echo 'Останавливаю старую версию...'
                    sh 'docker stop running-app || true'
                    sh 'docker rm running-app || true'
                    echo 'Запускаю новую версию...'
                    sh """
                        docker run -d \
                          -p 80:80 \
                          --name running-app \
                          ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }
    post {
        success {
            echo '✅ ВСЁ РАБОТАЕТ!'
            echo 'Приложение доступно по адресу: http://IP_вашего_сервера'
        }
        failure {
            echo '❌ Ошибка! Проверьте логи выше'
        }
    }
}
