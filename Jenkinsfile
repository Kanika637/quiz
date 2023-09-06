pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                
                git url: 'https://github.com/Kanika637/quiz.git', credentialsId: 'github_cred'
            }
        }

        stage('Run Basic Tests') {
            steps {
               
                sh 'npm install'
                script {
                    def testStatus = sh returnStatus: true, script: 'npm test'
                    if (testStatus != 0) {
                        currentBuild.result = 'FAILURE'
                        error('Tests failed. The build will not continue.')
                    }
                }
            }
        }

        stage('Build Web Application') {
            steps {
                
                sh 'npm run build'
            }
        }

        stage('Create Build Artifact') {
            steps {
               
                archiveArtifacts artifacts: '**/build/*', allowEmptyArchive: true
            }
        }

        stage('Deploy to AWS S3') {
            environment {
                AWS_ACCESS_KEY_ID = credentials('aws_access_key')
                AWS_SECRET_ACCESS_KEY= credentials('aws_secret_access_key')
            }
            steps {
                script {
                     sh 'aws configure set aws_access_key_id AWS_ACCESS_KEY_ID'
                    sh 'aws configure set aws_secret_access_key AWS_SECRET_ACCESS_KEY'
                    sh 'aws configure set default.region ap-south-1'

               
                    sh 'aws s3 cp ./build s3://web-app-deployment/ --recursive'
                }
            }
        }
    }

    post {
        success {
        
            echo 'Build and tests passed successfully. Application deployed to AWS S3.'
        }

        failure {
         
            echo 'Build or tests failed. Notify the team.'
        }
    }
}
