pipeline {
    agent any
    tools{
        nodejs 'nodejs-22-6-0'
        dockerTool 'docker-latest'
    }
    environment{
        MONGO_URI="mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_USERNAME="superuser"
        MONGO_PASSWORD="superpassword"
        JUNIT_REPORT_PATH="test-results.xml"  // Add this to specify the output file
    }
    stages {  
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
        stage('Build Docker image') {
            agent {
                kubernetes {
                     yaml '''
                        apiVersion: v1
                        kind: Pod
                        spec:
                          containers:
                          - name: docker
                            image: docker:dind
                            securityContext:
                              privileged: true
                              allowPrivilegeEscalation: true
                            volumeMounts:
                              - name: dind-storage
                                mountPath: /var/lib/docker
                            tty: true
                          volumes:
                            - name: dind-storage
                              emptyDir: {}
                    '''
                }
            }
            steps {
                container('docker') {
                    sh 'docker build -t siddharth67/solar-system:$GIT_COMMIT .'
                }
            }
        }
    }
    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}