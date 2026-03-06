pipeline{
    agent any
    tools{
        nodejs "Node18"
    }
    triggers{
        pollSCM('* * * * *')
    }
    stages{
        stage('Checkout'){
            steps{
                git url: 'https://github.com/Naresh1770/bluegreen-demo-app.git'
            }
        }
        stage('Install Dependecies'){
            steps{
                sh 'npm install'
            }
        }
        stage('Docker Image build'){
            steps{
                sh 'docker build -t image_demo:v1 .'
            }
        }
        stage('Docker Green'){
            steps{
                sh '''
                docker rm -f green || true
                docker run -d -p 3002:3000 --name green image_demo:v1
                '''
            }
        }
        stage('Switch traffic to Green'){
            steps{
                sh '''
                sudo sed -i 's/3001/3002/g' /etc/nginx/sites-available/default
                sudo systemctl restart nginx
                '''
            }
        }
        stage('Backup Build To S3'){
            steps{
                sh '''
                zip build-$BUILD_NUMBER.zip *
                aws s3 cp build-$BUILD_NUMBER.zip s3://naresh-devops-backups/
                '''
            }
        }
    }
    post{
        success{
            echo 'Green Blue Development is successful'
        }
    }
}