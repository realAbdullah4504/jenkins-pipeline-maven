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
        post {
            success {
                archiveArtifacts artifacts: '**/target/*.war'
                }
        }
    }