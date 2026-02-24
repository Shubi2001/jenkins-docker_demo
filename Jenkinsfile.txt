pipeline {
    agent any

    environment {
        SERVER = "shibu@192.168.1.181"
        APP_NAME = "demo-app"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/Shubi2001/jenkins-docker_demo.git'
            }
        }

        stage('Deploy to Linux Server') {
            steps {
                bat """
                scp -r * %SERVER%:/home/shibu/app

                ssh %SERVER% '
                    cd /home/shibu/app &&
                    docker stop %APP_NAME% || true &&
                    docker rm %APP_NAME% || true &&
                    docker build -t %APP_NAME% . &&
                    docker run -d -p 3000:3000 --name %APP_NAME% %APP_NAME%
                '
                """
            }
        }
    }
}