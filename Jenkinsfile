#!/usr/bin/env groovy
// configure .aws on jenkins so you can access s3 dmc-secrets2 bucket consult.md
node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:aquaclassic/dockerized-app'
            // Start services (Let docker-compose build containers for testing)
            sh "echo stat: 1: $(date) starting containers 'develop up' up "
            sh "./develop up -d"

            // Get composer dependencies

            sh "echo stat: 2: $(date) starting compoer install"
            sh "./develop composer install"

            // Create .env file for testing
            //sh "cp .env.example .env" awscli needs to be installed and setup
            sh "/var/lib/jenkins/.venv/bin/aws s3 cp s3://dmc-secrets2/env-ci .env"

            sh "echo stat: 3: $(date) start art key:gen"
            sh "./develop art key:generate"
            sh 'sed -i "s/REDIS_HOST=.*/REDIS_HOST=redis/" .env'
            sh 'sed -i "s/CACHE_DRIVER=.*/CACHE_DRIVER=redis/" .env'
            sh 'sed -i "s/SESSION_DRIVER=.*/SESSION_DRIVER=redis/" .env'

            sh "echo stat: 4: $(date) ENDING with seding .env"
        }
        stage('test') {
            sh "APP_ENV=testing ./develop test"
        }

        if( env.BRANCH_NAME == 'master' ) {
            stage('package') {
                sh './docker/build'
            }

            stage('deploy') {
              // use specifically create ssh key for this genkey
                sh 'ssh -i /var/lib/jenkins/.ssh/jenkins.pem ubuntu@172.31.24.28 /opt/deploy'
            }
        }
    } catch(error) {
        // Maybe some alerting?
        throw error
    } finally {
        // Spin down containers no matter what happens
        sh './develop down'
    }
}
