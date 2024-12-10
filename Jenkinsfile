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
                whoami
                echo "**********************************"
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
            
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'jenkins', keyFileVariable: 'SSH_KEY')]) {
                sh '''
                whoami
                echo "**********************************"
                mkdir -p ~/.ssh
                cp "$SSH_KEY" ~/.ssh/id_rsa
                chmod 600 ~/.ssh/id_rsa
                ssh-keyscan github.com >> ~/.ssh/known_hosts
                sudo mkdir -p /var/www/html
                sudo chown -R ubuntu:ubuntu /var/www
                sudo chmod -R 755 /var/www
                sudo echo "<IfModule dir_module>
                DirectoryIndex index.html index.jsp
                </IfModule>
                " | sudo tee -a /etc/apache2/apache2.conf
                '''
            }
                timeout(time: 5, unit: 'DAYS') {
                    input message: 'Deployment approved?'
                    dir("/var/www/html") {
                        unstash 'war'
                        sh """
                        whoami
                        echo "**********************************"
                        jar -xvf webapp.war
                        """
                    }
                }
            }
        }
    }    
}
