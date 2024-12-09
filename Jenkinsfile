pipeline {
    agent none  // Don't specify global agent, we'll specify per stage
    
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
            agent { label "agent" }  // Build on dev agent
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
                    agent { label "agent" }
                    steps{
                        echo 'this is test A'
                        sh "mvn test"
                    }
                }
                stage ('test B') {
                    agent { label "agent" }
                    steps{
                        echo 'this is test B'
                        sh "mvn test"
                    }
                }
            }
            post {
                success {
                    agent { label "agent" }
                    dir("webapp/target/") {
                        sh "pwd"
                        stash name: 'war', includes: '*.war'
                    }
                }
            }
        }
        stage('deploy_dev') {
            agent { label "agent" }  // Deploy on dev agent
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
