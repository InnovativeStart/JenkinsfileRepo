pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/InnovativeStart/Finlit.git'
        EC2_IP = '52.9.242.34'  // Replace with the actual EC2 IP address
        SSH_KEY_PATH = '/home/ec2-user/.ssh/jenkins.pem'  // Set the correct path to your private SSH key
        MAVEN_HOME = '/opt/maven' // Path to Maven (update if necessary)
        PATH = "$MAVEN_HOME/bin:$PATH"  // Add Maven to the PATH
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning application repository..."
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/learningmodules']], // Replace 'learningmodules' with your branch
                    userRemoteConfigs: [[
                        url: REPO_URL,
                        credentialsId: '51261412-9bf0-42cb-ba4c-256b77d10011' // Use Jenkins credentials
                    ]]
                ])
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                sh 'mvn clean package'  // Ensure 'mvn' is in PATH and Maven is correctly set up
            }
        }

        stage('Deploy') {
    		steps {
        		echo "Deploying the application..."
        		echo "SSH Key Path: $SSH_KEY_PATH"
        		echo "EC2 IP: $EC2_IP"
        		sh '''
        		ls -l $SSH_KEY_PATH  # Ensure the file exists and has the correct permissions
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
