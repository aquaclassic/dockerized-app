#!/usr/bin/env groovy
// configure .aws on jenkins so you can access s3 dmc-secrets2 bucket consult.md
node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:aquaclassic/dockerized-app'
            // Start services (Let docker-compose build containers for testing)
            sh "./develop up -d"

            // Get composer dependencies
            sh "./develop composer install"

            // Create .env file for testing
            //sh "cp .env.example .env"
            sh "/var/lib/jenkins/.venv/bin/aws s3 cp s3://dmc2-secrets2/env-ci .env"
            sh "./develop art key:generate"
            sh 'sed -i "s/REDIS_HOST=.*/REDIS_HOST=redis/" .env'
            sh 'sed -i "s/CACHE_DRIVER=.*/CACHE_DRIVER=redis/" .env'
            sh 'sed -i "s/SESSION_DRIVER=.*/SESSION_DRIVER=redis/" .env'
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
