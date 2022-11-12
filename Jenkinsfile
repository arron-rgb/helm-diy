properties([pipelineTriggers([githubPush()])])

pipeline {
    agent any

    environment {
        REPO = "csye7125-fall2022-group06/helmchart"
        GH_TOKEN = credentials('github_token')
        GITHUB_TOKEN = credentials('github_token')
        GIT_BRANCH = "main"
        GITHUB_USER = "arron-rgb"
        GITHUB_ACTIONS_CREDENTIAL = credentials("GITHUB_ACTIONS_CREDENTIAL")
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout([
                 $class: 'GitSCM',
                 branches: [[name: 'main']],
                 userRemoteConfigs: [[
                    url: 'https://github.com/csye7125-fall2022-group06/helmchart.git',
                    credentialsId: 'GITHUB_ACTIONS_CREDENTIAL',
                 ]]
                ])
            }
        }


        stage('Install semantic-release and plugins') {
            steps {
                sh '''
                npm install --save-dev semantic-release
                npm install @semantic-release/git @semantic-release/changelog @semantic-release/exec -D
                npm init release-it -y
                '''
            }
        }

        stage('Update repo') {
            steps {
                sh '''
                    git remote rm origin
                    git remote add origin https://${GITHUB_USER}:${GITHUB_TOKEN}@github.com/csye7125-fall2022-group06/helmchart.git
                    git symbolic-ref HEAD refs/heads/main
                    git pull origin main
                    chmod 777 ./verify.sh
                '''
            }
        }
        
        
        stage('Tag and update version') {
            steps {
                    sh '''
                    # Run optional required steps before releasing
                    npx semantic-release --debug
                    '''
            }
        }
        
         stage('Release') {
            steps {
                sh 'npx release-it -VV  --no-increment --no-git --github.release --github.update --no-npm --no-github.web --ci --no-github.draft --github.skipChecks'
            }
        }
    }
}
