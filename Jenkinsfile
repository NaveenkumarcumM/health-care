pipeline {
    agent { label 'slave-1' }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
    }

    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git 'https://github.com/NaveenkumarcumM/health-care.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Perform Application Build'
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                echo 'Perform Docker Build'
                sh "docker build -t naveencum98/health-care:${BUILD_NUMBER} ."
                sh "docker tag naveencum98/health-care:${BUILD_NUMBER} naveencum98/health-care:latest"
                sh 'docker image list'
            }
        }
        stage('Login to Dockerhub') {
            steps {
                echo 'Login to DockerHub'
                withCredentials([usernamePassword(credentialsId: 'dockerloginid', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                }
            }
        }
        stage('Publish the Image to Dockerhub') {
            steps {
                echo 'Publish to DockerHub'
                sh "docker push naveencum98/health-care:latest"
            }
        }
		stage('Deploy to Kubernetes Cluster') {
			steps {
				script {
				  sshPublisher(publishers: [sshPublisherDesc(configName: '', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/home/devopsadmin', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'ab')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
					}
                }
		    }

    }
}
