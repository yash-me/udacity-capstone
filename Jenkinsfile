pipeline {
    agent any
    stages {

		    stage('Lint HTML') {
			    steps {
				    sh 'tidy -q -e *.html'
				    sh 'echo $USER'
			    }
		    }
        
    
            stage('Build Docker Image') {
                steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    }
                }
            }

            stage('Deploy Image') {
                steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                        }
                    }
                }
            }

            stage('Configure and Build Kubernetes Cluster'){
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'ansible-playbook  ./plybook/clustercreate.yml'
                    }
                }
            }

            stage('Deploy Updated Image to Cluster'){
                steps {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'sudo kubectl apply -f  ./deploy/dploy.yml'
					sh 'sudo kubectl apply -f  ./deploy/lbalancer.yml'
                }
            }
        }
    }
}