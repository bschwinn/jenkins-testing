pipeline {

    agent any

    stages {

        stage ('build') {
            agent { label 'james-bond' }
            steps {
                checkout scm
                script {
                    GIT_SHORT_SHA = sh ( script: "git rev-parse --short HEAD", returnStdout: true ).trim()
                    VERSION = sh ( script: "node -p \"require('./package.json').version\"", returnStdout: true ).trim()
                    sh "mkdir ./dist"
                    sh "./jenkins.sh ${GIT_SHORT_SHA} ${VERSION} > ./dist/jenkins.out.txt"
                }
                build job: "deploy-dservice", 
                    parameters: [
                        string(name: 'jobName', value: ${JOB_NAME}),
                        string(name: 'version', value: ${VERSION}-${BUILDNUMBER})
                    ]
            }
        }

        stage ('feature') {
            agent { label 'james-bond' }
            when { branch "feature/*" }
            steps {
                checkout scm
                sh "./jenkins.sh test"
            }
        }
    }
}
