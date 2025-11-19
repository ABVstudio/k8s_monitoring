pipeline {
    agent {
         node {
          label 'udh-1'
       }
    }
    
    environment {
        Name_inf   = 'k8s_monitoring'
        URL_inf    = 'git@github.com:ABVstudio/k8s_monitoring.git'
        REGION     = 'us-east-1'
        NAMESPACE  = 'monitoring'
    }

    stages {
        stage('download') {
            steps {
                sh "if [ -d ${Name_inf} ]; then cd ${Name_inf} && git pull ;else git clone ${URL_inf}; fi"
            }
        }
        stage('kubectl update config') {
            steps {
                sh "aws eks update-kubeconfig --region ${REGION} --name ${CLUSTER}"
            }
        }
        stage('kubectl check|create namespace') {
            steps {
                sh "if [\$(kubectl get namespace | grep ${NAMESPACE}) -eq '']; then kubectl create namespace ${NAMESPACE}; fi"
            }
        }
        stage('prometgeus\grafana update|install') {
            steps {
                sh "helm upgrade --install -f ${Name_inf}/myvalues.yaml monitoring oci://ghcr.io/prometheus-community/charts/kube-prometheus-stack --namespace ${NAMESPACE}"
            }
        }
    }
}

