pipeline {

    agent any

    stages {

        stage ('build') {
            agent { label 'james-bond' }
            when { branch "develop" }
            steps {
                checkout scm
                script {
                    GIT_SHORT_SHA = sh ( script: "git rev-parse --short HEAD", returnStdout: true ).trim()
                    sh "jenkins.sh ${GIT_SHORT_SHA}"
                }
            }
        }

        stage ('feature') {
            agent { label 'james-bond' }
            when { branch "feature/*" }
            steps {
                checkout scm
                sh "jenkins.sh test"
            }
        }
    }
}
