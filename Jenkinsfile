pipeline {
    agent { label 'agent' }
    
    tools {
        maven 'maven'
    }
    environment {
        NAME = "abdullah"
    }
    parameters {
        string defaultValue:'javed',name:'LASTNAME'
        choice choices: ['dev','prod'],description: 'choose env',name: 'ENV'
    }
    stages {
        stage('build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
                echo "hello $NAME ${params.LASTNAME}"
                script {
                    file = load "script.groovy"
                    file.hello()
                }
            }   
        }
        stage('test'){
            parallel {
                stage ('test A') {
                    steps{
                        echo 'this is test A'
                        sh "mvn test"
                    }
                }
                stage ('test B') {
                
                    steps{
                        echo 'this is test B'
                        sh "mvn test"
                    }
                }
            }
            post {
                success {
                    dir("webapp/target/") {
                        sh "pwd"
                        stash name: 'war', includes: '*.war'
                    }
                }
            }
        }
        stage('deploy_dev') {
            when {
                expression { params.ENV == 'dev' } 
                beforeAgent true
            }
            steps {
                dir("/var/www/html") {
                    unstash 'war'
                }
                sh """
                cd /var/www/html/
                jar -xvf webapp.war
                """
            }
        }
        stage('deploy_prod') {
            agent { label "prod-server" }  // Deploy on production agent
            when {
                expression { params.ENV == 'prod' }
                beforeAgent true
            }
            withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh-key', keyFileVariable: 'SSH_KEY')]) {
            sh '''
            mkdir -p ~/.ssh
            cp "$SSH_KEY" ~/.ssh/id_rsa
            chmod 600 ~/.ssh/id_rsa
            ssh-keyscan github.com >> ~/.ssh/known_hosts
            '''
            }
            steps {
                timeout(time: 5, unit: 'DAYS') {
                    input message: 'Deployment approved?'
                    dir("/var/www/html") {
                        unstash 'war'
                        sh """
                        jar -xvf webapp.war
                        rm webapp.war
                        """
                    }
                }
            }
        }
    }    
}
