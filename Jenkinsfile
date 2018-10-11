pipeline {
    agent any
    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello World"'
				sh 'printenv'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
		stage('Test') {
            steps {
                sh 'echo "Succ!"; exit 0'
            }
        }
		stage('Deploy - Staging') {
            steps {
                retry(3) {
                    sh './flakey-deploy.sh'
                }

                timeout(time: 3, unit: 'MINUTES') {
                    sh './health-check.sh'
                }
            }
        }
		stage('Sanity check') {
			steps {
				input "进行线上部署"
            }
		}
		stage('Deploy - Production') {
        	steps {
				sh 'echo "deployed"'
			}
		}
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
			mail to: 'sunyonglu@lvwan.com',
             	 subject: "Failed Pipline: ${currentBuild.fullDisplayName}",
             	 body: "abnormal ${env.BUILD_URL}"
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
