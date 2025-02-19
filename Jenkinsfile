pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/InnovativeStart/Finlit.git'
        EC2_IP = '52.9.201.2'  // Ensure correct App Server IP
        SSH_KEY_PATH = '/var/lib/jenkins/.ssh/jenkins.pem'  // Correct SSH key location
        MAVEN_HOME = '/opt/maven'  // Update if necessary
        PATH = "$MAVEN_HOME/bin:$PATH"  // Ensure Maven is accessible
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning application repository..."
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/learningmodules']],  // Ensure branch exists
                    userRemoteConfigs: [[
                        url: "https://github.com/InnovativeStart/Finlit.git",
                        credentialsId: '16c4f6e5-c92a-4484-a4d5-9d65617fd3d0' 
                    ]]
                ])
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                sh '''
                export PATH=$MAVEN_HOME/bin:$PATH  # Ensure correct Maven path
                mvn clean package -DskipTests  # Skip tests for faster builds
                '''
            }
        }

        stage('Deploy') {
    steps {
        echo "Deploying the application..."
        echo "SSH Key Path: $SSH_KEY_PATH"
        echo "EC2 IP: $EC2_IP"
        sh '''
        if [ ! -f $SSH_KEY_PATH ]; then echo "SSH Key not found"; exit 1; fi
        chmod 600 $SSH_KEY_PATH  # Ensure correct permissions
        ls -l $SSH_KEY_PATH  # Verify key exists
        
        echo "Transferring JAR to EC2..."
        scp -o StrictHostKeyChecking=no -i $SSH_KEY_PATH target/*.jar ec2-user@$EC2_IP:/home/ec2-user/
        
        echo "Restarting Application..."
        ssh -o StrictHostKeyChecking=no -i $SSH_KEY_PATH ec2-user@$EC2_IP << 'EOF'
        pkill -f "java -jar" || echo "No running process found"
        nohup java -jar /home/ec2-user/*.jar > /home/ec2-user/app.log 2>&1 &
        exit
        EOF
        '''
    }
}
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed! Check logs."
        }
    }
}
