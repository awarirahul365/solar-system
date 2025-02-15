pipeline {
    agent any
    tools{
        nodejs 'nodejs-22-6-0'
    }
    environment{
        MONGO_URI="mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_USERNAME="superuser"
        MONGO_PASSWORD="superpassword"
        JUNIT_REPORT_PATH="test-results.xml"  // Add this to specify the output file
    }
    stages {  
        stage('Install Docker') {
            steps {
                sh '''
                    if ! command -v docker &> /dev/null; then
                        curl -fsSL https://get.docker.com -o get-docker.sh
                        sh get-docker.sh
                        usermod -aG docker jenkins
                        systemctl enable docker || true
                        systemctl start docker || true
                    fi
                '''
            }
        }
        stage('Node Version and checkout') {
            steps {
                sh '''
                    ls -R
                    node -v
                    npm -v
                '''
            }
        }
        stage('Install dependencies') {
            steps {
                sh '''
                    npm install --no-audit
                '''
            }
        }
        stage('Unit Testing'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'mongo-db-credential', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'This will be fixed later', stageResult: 'UNSTABLE') {
                        sh 'npm test'
                    }
                }
            }
        }
        stage('Code Coverage'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'mongo-db-credential', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'This will be fixed later', stageResult: 'UNSTABLE') {
                        sh 'npm run coverage'
                    }
                }
            }
        }
        stage('Build Docker image'){
            steps{
                sh 'docker build -t siddharth67/solar-system:$GIT_COMMIT .'
            }
        }
    }
    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}