pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/InnovativeStart/Finlit.git'
        EC2_IP = '52.9.242.34'  // Replace with the actual EC2 IP address
        SSH_KEY_PATH = '/home/ec2-user/.ssh/jenkins.pem'  // Set the correct path to your private SSH key
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning application repository..."
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/learningmodules']], // Replace 'main' with your branch
                    userRemoteConfigs: [[
                        url: REPO_URL,
                        credentialsId: '64861eaa-5ef7-4f28-b73b-cf0328176c87' // Use Jenkins credentials
                    ]]
                ])
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                sh './mvn clean package' // Modify as per your project
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying the application..."
                sh '''
                scp -o StrictHostKeyChecking=no -i $SSH_KEY_PATH target/*.jar ec2-user@$EC2_IP:/home/ec2-user/
                ssh -i $SSH_KEY_PATH ec2-user@$EC2_IP "nohup java -jar /home/ec2-user/*.jar > app.log 2>&1 &"
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
