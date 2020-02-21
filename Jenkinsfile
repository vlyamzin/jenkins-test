pipeline {
    agent {docker {
            image 'node:latest' 
            args '-p 3000:3000' 
        }}
    environment {
        HOME="."
    }
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
                script {
                    def input = readJSON file: 'package.json'
                    input['version'] = env.TAG
                    writeJSON file: 'package.json', json: input, pretty: 4
                    sh(returnStdout: true, script: "git tag")
                    sh(returnStdout: true, script: "git commit -a --author=\"vlyamzin <vlad.lyamzin@avenga.com>\" -m=\"New Version!\"")
                }
            }
        }
    }
}