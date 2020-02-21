pipeline {
    agent {docker {
            image 'node:latest' 
            args '-p 3000:3000' 
        }}
    stages {
        stage('init') {
            steps {
                script {
                    env.APP_VERSION = sh(returnStdout: true, script: "node -p -e \"require('./package.json').version\"").trim()
                    env.TAG = sh(returnStdout: true, script: "git tag").trim()
                }
            }
        }
        stage('compare') {
            when {
                expression { env.APP_VERSION != env.TAG }
            }
            steps {
                echo 'Need to update version!'
            }
        }
    }
}