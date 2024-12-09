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
                        stash name: 'war', includes: '*.war'
                    }
                }
            }
        }
        stage('deploy_dev')
        {
            steps {
                echo "deploying to dev"
            }
        }
    }    
}