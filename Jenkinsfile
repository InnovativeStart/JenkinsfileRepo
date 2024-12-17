pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning application repository..."
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // Replace 'main' with your branch
                    userRemoteConfigs: [[
                        url: 'https://github.com/InnovativeStart/Finlit.git',
                        credentialsId: 'github-pat' // Use Jenkins credentials
                    ]]
                ])
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                sh './mvnw clean package' // Modify as per your project
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying the application..."
                sh '''
                scp -o StrictHostKeyChecking=no -i /path/to/key.pem target/*.jar ec2-user@<app-server-ip>:/home/ec2-user/
                ssh -i /path/to/key.pem ec2-user@<app-server-ip> "nohup java -jar /home/ec2-user/*.jar > app.log 2>&1 &"
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
