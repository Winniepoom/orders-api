pipeline {
    agent any
    environment {
            ECR_URI = "458818121281.dkr.ecr.ap-southeast-1.amazonaws.com"
    }
    stages {              // ONE stages block
    
        stage('Checkout') {
            steps { 
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    env.IMAGE_TAG = "${env.BUILD_NUMBER}-${env.GIT_COMMIT.take(7)}"
                    sh "docker build -t ${ECR_URI}/orders-api:${env.IMAGE_TAG} ."
                }
            }
        }
        stage('smoke test') {
            steps {
            sh "docker run -d --name smoke-${env.BUILD_NUMBER} ${ECR_URI}/orders-api:${env.IMAGE_TAG}"
            sh 'SIP=$(docker inspect -f "{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}" smoke-$BUILD_NUMBER); curl -s --retry 5 --retry-delay 1 --retry-connrefused http://$SIP:8088/healthz | grep "ok"'
            sh "docker rm -f smoke-${env.BUILD_NUMBER}"          
            }
        }
        stage('Push') {
            when { branch 'main' }
            steps {
            withCredentials([usernamePassword(credentialsId: 'ecr-ci-key',  
                    usernameVariable: 'AWS_ID', passwordVariable: 'AWS_SECRET')]) {
                sh '''
                export AWS_ACCESS_KEY_ID="$AWS_ID"
                export AWS_SECRET_ACCESS_KEY="$AWS_SECRET"
                aws ecr get-login-password --region ap-southeast-1 | docker login "$ECR_URI" --username AWS --password-stdin
                docker push "$ECR_URI/orders-api:$IMAGE_TAG"
                '''
            }
    }
}/*
        stage('Bump') {
            when { branch 'main' }
            steps {
                withCredentials([usernamePassword(credentialsId: 'gitops-write-token',
                        usernameVariable: 'GH_USER', passwordVariable: 'GH_TOKEN')]) {
                    sh '''
                        rm -rf gitops-repo
                        git clone https://$GH_USER:$GH_TOKEN@github.com/Winniepoom/gitops-repo.git
                        cd gitops-repo
                        sed -i "s|tag: .*|tag: $IMAGE_TAG|" charts/orders-api/values.yaml
                        git config user.email "jenkins-ci@orders-api.local"
                        git config user.name "jenkins-ci"
                        git add charts/orders-api/values.yaml
                        git commit -m "bump orders-api to $IMAGE_TAG (build ${BUILD_NUMBER})" || echo "no changes to commit"
                        git push
                    '''
                }
            }
        }*/
    }
}