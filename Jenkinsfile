pipeline {
    environment {
        registry = "luther007/jenkins-eks-automated"
        registryCredential = 'docker-hub-credentials'
        dockerImage = ''
        AWS_ACCESS_KEY_ID     = credentials('JenkinsAWSKey')
        AWS_SECRET_ACCESS_KEY = credentials('JenkinsAWSKeySecret')
        PATH = "/root/bin:${env.PATH}"
    }

    agent {
        docker {
            image 'luther007/cynerge_images:latest'
            args '-u root'
        }
    }

    stages {
    
        stage('Dependencies') {
            steps {
                echo 'Installing...'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'npm test'
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                    dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
        stage('Deploy Kube') {
            steps {
                // sh 'curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -'
                // sh 'echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list'
                // sh 'apt-get update'
                // sh 'apt-get install -y kubectl'
                // sh 'kubectl config --kubeconfig=cluster-config use-context ljoliff@cynerge-cluster-6.us-east-1.eksctl.io'
                // sh 'export KUBECONFIG=$KUBECONFIG:cluster-config'
                // sh 'curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.7/2019-03-27/bin/linux/amd64/aws-iam-authenticator'
                // sh 'chmod +x ./aws-iam-authenticator'
                // sh 'mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$HOME/bin:$PATH && cp ./aws-iam-authenticator /usr/bin/aws-iam-authenticator'
                // sh 'echo export PATH=$HOME/bin:$PATH >> ~/.bashrc'
                sh "export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID"
                sh "export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY"
                sh 'printenv'
                // sh 'cp cluster-config ~/.kube/'
                sh "kubectl set image deployment/jenkins-eks-automated mike1=luther007/jenkins-eks-automated:$BUILD_NUMBER --kubeconfig=cluster-config"
            }
        }
    }
    post { 
        always { 
            cleanWs()
        }
    }
}

