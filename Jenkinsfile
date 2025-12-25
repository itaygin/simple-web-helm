pipeline {
    agent any

    parameters {
        choice(
            name: 'ACTION',
            choices: ['deploy', 'destroy'],
            description: 'Select action to perform'
        )
        string(
            name: 'NAMESPACE',
            defaultValue: 'itay',
            description: 'Kubernetes namespace'
        )
        string(
            name: 'RELEASE_NAME',
            defaultValue: 'simple-web',
            description: 'Helm release name'
        )
    }

    environment {
        CHART_PATH = 'simple-web'
        KUBECONFIG = '/home/azureuser/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy') {
            when {
                expression { params.ACTION == 'deploy' }
            }
            steps {
                sh """
                    helm upgrade --install ${params.RELEASE_NAME} ${CHART_PATH} \
                        --namespace ${params.NAMESPACE}
                """
                echo "Deployed ${params.RELEASE_NAME} to namespace ${params.NAMESPACE}"
            }
        }

        stage('Destroy') {
            when {
                expression { params.ACTION == 'destroy' }
            }
            steps {
                sh """
                    helm uninstall ${params.RELEASE_NAME} \
                        --namespace ${params.NAMESPACE}
                """
                echo "Destroyed ${params.RELEASE_NAME} from namespace ${params.NAMESPACE}"
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully: ${params.ACTION}"
        }
        failure {
            echo "Pipeline failed: ${params.ACTION}"
        }
    }
}
