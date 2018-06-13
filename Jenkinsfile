pipeline {

    agent any

    stages {

        stage ('build') {
            agent { label 'linux-slave' }
            steps {
                script {
                    GIT_SHORT_SHA = sh ( script: "git rev-parse --short HEAD", returnStdout: true ).trim()
                    VERSION = sh ( script: "node -pe \"require('./package.json').version\"", returnStdout: true ).trim()
                    S3_LOC = env.DSERVICE_S3_ROOT_STAGING + "notifications/" + VERSION + "-" + env.BUILD_NUMBER
                    PREREL_VERSION = VERSION + "-alpha." + env.BUILD_NUMBER
                }
                sh "mkdir -p ./dist"
                sh "./jenkins.sh ${GIT_SHORT_SHA} ${VERSION} > ./dist/jenkins.out.txt"
                sh "echo ${VERSION} > ./dist/VERSION"
                sh "echo ${GIT_SHORT_SHA} > ./dist/GITSHA"
                echo "publishing pre-release version to npm: " + PREREL_VERSION
                sh "npm version --no-git-tag-version " + PREREL_VERSION
                sh "npm publish --tag alpha"
                sh "npm version --no-git-tag-version " + VERSION
                sh "aws s3 cp ./dist ${S3_LOC}/ --recursive --dryrun"
                // build job: "deploy-dservice", 
                //     parameters: [
                //         string(name: 'jobName', value: JOB_NAME),
                //         string(name: 'version', value: VERSION + "-" + String.valueOf(BUILD_NUMBER))
                //     ]
            }
        }

        stage ('feature') {
            agent { label 'linux-slave' }
            when { branch "feature/*" }
            steps {
                checkout scm
                sh "./jenkins.sh test"
            }
        }
    }
}
