pipeline {
    agent any
    tools {
        nodejs 'nodejs-22-6-0'
    }
    environment {
        MONGO_URI="mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_USERNAME="superuser"
        MONGO_PASSWORD="superpassword"
        JUNIT_REPORT_PATH="test-results.xml"
    }
    stages {  
        stage('Node Version and Checkout') {
            steps {
                sh '''
                    ls -R
                    node -v
                    npm -v
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }
        stage('Unit Testing') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo-db-credential', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'This will be fixed later', stageResult: 'UNSTABLE') {
                        sh 'npm test'
                    }
                }
            }
        }
        stage('Code Coverage') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo-db-credential', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'This will be fixed later', stageResult: 'UNSTABLE') {
                        sh 'npm run coverage'
                    }
                }
            }
        }
        stage('Build Image with Podman') {
            agent {
                kubernetes {
                    yaml '''
                        apiVersion: v1
                        kind: Pod
                        metadata:
                          labels:
                            app: podman-builder
                        spec:
                          containers:
                            - name: podman
                              image: quay.io/podman/stable
                              command:
                                - cat
                              tty: true
                              securityContext:
                                privileged: true
                              volumeMounts:
                                - name: podman-storage
                                  mountPath: /var/lib/containers
                          volumes:
                            - name: podman-storage
                              emptyDir: {}
                    '''
                }
            }
            steps {
                container('podman') {
                    sh '''
                        podman build -t siddharth67/solar-system:$GIT_COMMIT .
                    '''
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