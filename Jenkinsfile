@Library('common-lib@1.6') _
pipeline {
    agent any;
    environment {
        DOCKER_REPO = 'kyberorg/ng-samples'
    }
    parameters {
        string(name: 'DOCKER_TAG', defaultValue: "")
    }
    stages {
        stage('Docker') {
            steps {
                script {
                    def dockerTag = params.DOCKER_TAG;
                    if(dockerTag.trim().equals("")) {
                        dockerTag = env.BRANCH_NAME;
                    }

                    def tags = [];
                    tags << dockerTag;

                    dockerBuild(repo: env.DOCKER_REPO, tags: tags);
                    dockerLogin(creds: 'hub-docker');
                    dockerPush();
                    dockerLogout();
                    dockerClean();
                }
            }
        }
        stage('Deploy to K8S') {
            steps {
              script{
                  deployToKube(
                    namespace: 'dev-ng-samples',
                    workloadName: 'ng-samples-app',
                    imageRepo: env.DOCKER_REPO,
                    imageTag: env.BRANCH_NAME
                  )
              }
            }
        }
    }
    post {
        always {
            cleanWs();
        }
    }
}
