pipeline {
    agent {
        label 'agent'
    }
    // choice parameters
    parameters {
        choice choices: ['dev','prod'],description: 'choose env',name: 'ENV'
    }
    tools {
        maven 'maven'
    }
    environment {
        NAME = "abdullah"
    }
    parameters {
        string defaultValue:'javed',name:'LASTNAME'
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
                    dir("webapp/target/")
                    {
                        stash name: 'war', includes: '*.war'
                    }
                }
            }
        }
        stage('deploy_dev')
        {
            when {
                expression {
                    return params.ENV == 'dev'
                    }
                    steps {
                        echo "deploying to dev"
                        sh "hostname -i"
                    }
                }
        }
    }    
}