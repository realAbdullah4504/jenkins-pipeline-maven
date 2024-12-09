pipeline {
    agent { label "agent"}
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
        stage('build')
        {
            steps {
                sh 'mvn clean package -DskipTests=true'
                echo "hello $NAME ${params.LASTNAME}"
                }
                script {
                    file = load "script.groovy"
                }
                
        }
        stage('test'){
            parallel {
                stage ('test A')
                {
                    steps{
                        echo 'this is test A'
                        sh "mvn test"
                    }
                }
                stage ('test B')
                {
                    steps{
                        echo 'this is test B'
                        sh "mvn test"
                    }
                }
            }
            post {
                success {
                    // archiveArtifacts artifacts: '**/target/*.war'
                    dir("webapp/target/")
                    {
                        sh "pwd"
                        stash name: 'war', includes: '*.war'
                    }
                }
            }
        }
        stage('deploy_dev')
        {
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
        stage('deploy_prod')
        {
            when {
                expression { params.ENV == 'prod' }
                beforeAgent true
            }
            steps {
                timeout(time: 5, unit: 'Days') {
                    input message: 'Deployment approved?', ok: 'Deploy'

                dir("/var/www/html") {
                    unstash 'war'
                    sh """
                    cd /var/www/html/
                    jar -xvf webapp.war
                    """
                    sh "pwd"
                }
                }
            }
        }
    }    
}