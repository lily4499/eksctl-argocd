pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('lil_AWS_Access_key_ID')
        AWS_SECRET_ACCESS_KEY = credentials('lil_AWS_Secret_access_key')
        AWS_REGION = "us-east-1"
        EKS_CLUSTER_NAME = 'lil_cluster'
        KUBECONFIG_PATH = '/var/lib/jenkins/.kube/config'
        ARGOCD_APP_NAME = 'lili-app'
    }

    stages {
	stage('Checkout') {
            steps {
           	git branch: 'main', url: 'https://github.com/lily4499/eksctl-argocd.git'
  
            }
        }

        stage ("Install EKS") {
            steps {
                bat "C:\\ProgramData\\chocolatey\\bin\\eksctl create cluster --name ${EKS_CLUSTER_NAME} --region ${AWS_REGION}"
 
            }
        }
  

        stage('Install ArgoCD in EKS') {
            steps {
                script {
                    // Install ArgoCD in EKS
		    bat "kubectl create namespace argocd"
                    bat "kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml"
                }
            }
        }

        stage('ArgoCD: Deploy Application') {
            steps {
                script {
                    // Wait for ArgoCD pods to be ready

                    bat "kubectl wait --for=condition=Ready pod -l app.kubernetes.io/name=argocd-server -n argocd --timeout=300s"

                    // Create or sync the application in ArgoCD
                    bat "argocd app create $ARGOCD_APP_NAME --repo https://github.com/lily4499/argo-kubernetes.git --path ./ --dest-server https://kubernetes.default.svc --dest-namespace default"
                    bat "argocd app sync $ARGOCD_APP_NAME"
                }
            }
        }
    }

 //   post {
   //     always {
            // Cleanup steps if needed
    //    }
 //   }
}
