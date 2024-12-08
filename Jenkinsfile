pipeline {
    agent {
        label 'agent'
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
                    sh 'mvn clean package'
                    echo "hello $NAME ${params.LASTNAME}"
                    }
                
            }
        }
        stage('test'){
            parallel {
                stage ('test A')
                {
                    steps{
                        echo 'this is test A'
                    }
                }
                stage ('test B')
                {
                    steps{
                        echo 'this is test B'
                    }
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.war'
                    }
            }
        }
    }