pipeline {
    agent none
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
            agent { label 'agent' }
            steps {
                sh 'mvn clean package -DskipTests=true'
                echo "hello $NAME ${params.LASTNAME}"
                }
                
        }
        stage('test'){
            parallel {
                stage ('test A')
                {
                    agent { label 'agent' }
                    steps{
                        echo 'this is test A'
                        sh "mvn test"
                    }
                }
                stage ('test B')
                {
                    agent { label 'agent' }
                    steps{
                        echo 'this is test B'
                        sh "mvn test"
                    }
                }
            }
            post {
                success {
                    // archiveArtifacts artifacts: '**/target/*.war'
                    node('agent')
                    script{
                        dir("webapp/target/") {
                        stash name: 'war', includes: '*.war'
                        }
                    }
                }
            }
        }
        stage('deploy_dev')
        {
            agent { label 'agent' }
            steps {
                echo "deploying to dev"
            }
        }
    }    
}