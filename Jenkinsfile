pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Git Checkout'
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Devrajnarayanan/jenkins_testing_repo.git']])
                sh 'ls -lrth'
                sh 'git branch'
                //cleanWs cleanWhenAborted: false, cleanWhenFailure: false, cleanWhenNotBuilt: false, cleanWhenUnstable: false
                //sh 'ls -lrth'
            }
        }
        stage('Build') {
		when {
   			branch 'stagging_branch'
		}
		steps {
                echo 'Building InProgress...'
				sh '''
                rm -rf build/ node_modules/
				npm install
                SWA_CLI_DEPLOYMENT_TOKEN=$(az staticwebapp secrets list --name Jenkins-DevOps --query "properties.apiKey" | sed 's/"//g')
				echo $SWA_CLI_DEPLOYMENT_TOKEN
				npm run build
				ls -lrth
                '''
            }
        }
        stage('Deploy') {
		when {
   			'stagging_branch'
		      }
		steps {
                echo 'Deploying to Azure....'
                sh '''
                SWA_CLI_DEPLOYMENT_TOKEN=$(az staticwebapp secrets list --name Jenkins-DevOps --query "properties.apiKey" | sed 's/"//g')'
                echo $SWA_CLI_DEPLOYMENT_TOKEN
				swa deploy ./build --env=Production --deployment-token=$SWA_CLI_DEPLOYMENT_TOKEN --verbose=silly --no-use-keychain
				'''
            }
        }
        stage('CleanUp') {
		steps {
                echo 'Cleaning Workspace'
                cleanWs cleanWhenAborted: false, cleanWhenFailure: false, cleanWhenNotBuilt: false, cleanWhenUnstable: false
                sh 'ls -lrth'
            }
        }
    }
}

