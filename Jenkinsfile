pipeline {
    agent any

    environment {
        DB_URL = 'mysql+pmysql://usr:ptwd@host:3306/db'
        DISABLE_AUTH = true
    }

    stages {
        stage('Сборка') {
            steps {
                echo 'Сборка приложения...'
                sh '''
                    echo "Этот блок содержит многострочные шаги"
                    ls -lh
                    echo "URL базы данных: ${DB_URL}"
                    echo "DISABLE_AUTH: ${DISABLE_AUTH}"
                    env
                    echo "Запуск задачи с номером сборки: ${env.BUILD_NUMBER} на ${env.JENKINS_URL}"
                '''
            }
        }

        stage('Тестирование') {
            steps {
                echo 'Тестирование приложения...'
                sh 'chmod +x ./smoke-tests'
                sh './smoke-tests'
            }
        }

        stage('Деплой на стейджинг') {
            steps {
                echo 'Деплой на стейджинг...'
                sh 'chmod +x ./deploy'
                sh './deploy staging'
            }
        }

        stage('Проверка работоспособности') {
            steps {
                echo 'Проверка работоспособности...'
            }
        }

        stage('Деплой на продакшн') {
            steps {
                echo 'Деплой на продакшн...'
                sh './deploy production'
            }
        }
    }

    post {
        always {
            echo 'Этот блок будет выполняться независимо от статуса завершения'
            cleanWs()
        }
        success {
            echo 'Это будет выполняться, если сборка успешна'
            mail to: 'valentinevelichcko@yandex.ru',
                 subject: "${env.JOB_NAME} - Сборка № ${env.BUILD_NUMBER} успешна",
                 body: "Сборка прошла успешно. Подробности: ${env.BUILD_URL}"
        }
        failure {
            echo 'Это будет выполняться, если задача провалилась'
            mail to: 'valentinevelichcko@yandex.ru',
                 subject: "${env.JOB_NAME} - Сборка № ${env.BUILD_NUMBER} провалилась",
                 body: "Для получения дополнительной информации о провале пайплайна, проверьте консольный вывод по адресу ${env.BUILD_URL}"
        }
        unstable {
            echo 'Это будет выполняться, если статус завершения был нестабильный'
        }
        changed {
            echo 'Это будет выполняться, если состояние пайплайна изменилось'
        }
    }
}