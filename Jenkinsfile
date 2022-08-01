pipeline {
    agent none
    environment {
        projectName = "juiceshop"
        GOOGLE_CREDENTIALS = credentials('NPE_GCR_CARBON_CREDENTIALS')
        repository = "gcr.io/gcp-ushi-carbon-svcs-dev/${projectName}"
    }
    stages {
        stage('Docker Build and Push Image') {
            agent { label 'docker' }
            steps {
                script {
                    COMMIT_ID = sh(returnStdout: true, script: 'git rev-parse HEAD')
                    IMAGE_TAG = "JENKINS-${env.BUILD_ID}_${BRANCH_NAME}_${COMMIT_ID}"
                    sh 'docker login -u _json_key -p "$GOOGLE_CREDENTIALS" https://gcr.io'

                    sh "docker build . -t ${repository}:${IMAGE_TAG}"
                    sh "docker push ${repository}:${IMAGE_TAG}"
                }
            }
        }
    }
    post {
        failure {
            script {
                mail (to: 'morgan.eason@lowes.com',
                        subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) failed.",
                        body: "Please visit ${env.BUILD_URL} for further information."
                );
            }
        }
    }
}
