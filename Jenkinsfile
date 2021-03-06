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
                expression { (env.APP_VERSION != env.TAG) && (GIT_BRANCH == "origin/master") }
            }
            steps {
                echo 'Need to update version!'
                script {
                    def input = readJSON file: 'package.json'
                    input['version'] = env.TAG
                    writeJSON file: 'package.json', json: input, pretty: 4
                }
                sh '''
                    git status
                    git config user.name "vlyamzin"
                    git config user.email "vlyamzin@corevalue.net"
                    git commit -am "New version $TAG"
                '''
                script {
                    withCredentials([usernamePassword(credentialsId: 'vlyamzin-github', usernameVariable: 'username', passwordVariable: 'password')])
                    {
                        script {
                            env.encodedPass=URLEncoder.encode(password, "UTF-8")
                        }
                        sh('git push https://${username}:${encodedPass}@github.com/vlyamzin/jenkins-test.git HEAD:master')
                    }
                }
            }
        }
    }
    post {
      always {
          cleanWs()
      }
    
    }
}