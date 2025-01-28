pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/InnovativeStart/Finlit.git'
        EC2_IP = '52.9.242.34'  // Replace with actual EC2 IP
        SSH_KEY_PATH = '/var/lib/jenkins/.ssh/jenkins.pem'  // Ensure correct permissions
        MAVEN_HOME = '/opt/maven'  // Update if necessary
        PATH = "$MAVEN_HOME/bin:$PATH"  // Add Maven to PATH
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning application repository..."
                withCredentials([string(credentialsId: 'github-new-token', variable: 'GITHUB_TOKEN')]) {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/learningmodules']],  // Ensure correct branch name
                        userRemoteConfigs: [[
                            url: "https://$GITHUB_TOKEN@github.com/InnovativeStart/Finlit.git",
                            credentialsId: 'github-new-token'
                        ]]
                    ])
                }
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                sh 'mvn clean package'  // Ensure 'mvn' is in PATH
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying the application..."
                echo "SSH Key Path: $SSH_KEY_PATH"
                echo "EC2 IP: $EC2_IP"
                sh '''
                chmod 600 $SSH_KEY_PATH  # Ensure the SSH key has correct permissions
                ls -l $SSH_KEY_PATH  # Check if the key exists
                scp -o StrictHostKeyChecking=no -i $SSH_KEY_PATH target/*.jar ec2-user@$EC2_IP:/home/ec2-user/
                ssh -o StrictHostKeyChecking=no -i $SSH_KEY_PATH ec2-user@$EC2_IP "nohup java -jar /home/ec2-user/*.jar > app.log 2>&1 &"
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
