// VARIABLES DE CONFIGURACION:

def NODE_VERSION = '24'
def DOCKER_IMAGE_REPOSITORY_NAME = 'fercdevv/backend-sonar-node-api'
def DOCKER_IMAGE_TAG = 'latest'
def AWS_REGION = 'us-east-1'
def AWS_CLOUDFORMATION_STACK_NAME = 'my-fargate-stack'

def AWS_VPC_ID = 'vpc-0a3cd97730bc5332b'
def AWS_SUBNETS = 'subnet-084371090cddeab02,subnet-08ee9a8fbd634ee19,subnet-0233ac4ffbd7f09c9,subnet-081e593c9cc8b1376,subnet-0790db53816907cc4,subnet-0a8702af878a5a838'



def buildAndTest() {
    stage('Install dependencies') { 
        sh 'npm install'
    }

    stage('Run tests') { 
        sh 'npm test'
    }
}

def dockerLogin () {
    withCredentials([
        usernamePassword(
            credentialsId: 'docker-hub-credentials',
            usernameVariable: 'DOCKER_HUB_USERNAME',
            passwordVariable: 'DOCKER_HUB_TOKEN'
        )
    ])
    {
        sh '''
            echo $DOCKER_HUB_TOKEN | docker login \
                --username $DOCKER_HUB_USERNAME \
                --password-stdin
        '''
    }
}


node {
    def dockerImageTag = "TEMPORAL_ID_${env.BUILD_ID}"//env.GIT_COMMIT
    def remoteImage = "${DOCKER_IMAGE_REPOSITORY_NAME}:${dockerImageTag}"

    try {
        stage("Validate node, build and test") {
            docker.image("node:${NODE_VERSION}-alpine").inside {
                stage("Listar archivos") {
                    sh "ls -la"
                }

                stage('Check node version (agent node)') {
                    sh 'node -v'
                }

                buildAndTest()
            }
        }

        stage("Docker build and push") {
            docker.image('docker:27-cli').inside {
                dockerLogin()

                echo "Building and pushing Docker image: ${remoteImage}"

                sh """
                    docker build -t ${remoteImage} .
                    docker push ${remoteImage}
                """
            }
        }

        stage("Promote Image to latest") {
            dockerLogin()

            docker.image('docker:27-cli').inside {
                stage("Pull Image") {
                    sh "docker pull ${remoteImage}"
                }

                stage("Tag Image as latest") {
                    sh "docker tag ${remoteImage} ${DOCKER_IMAGE_REPOSITORY_NAME}:${DOCKER_IMAGE_TAG}"
                }

                stage("Push Image as latest") {
                    sh "docker push ${DOCKER_IMAGE_REPOSITORY_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage("Deploy to AWS Fargate") {
            withCredentials([
                $class: 'AmazonWebServicesCredentialsBinding',
                credentialsId: 'aws-credentials',
                accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
            ]) {
                docker.image('amazon/aws-cli').inside {
                    withEnv([
                        "AWS_DEFAULT_REGION=${AWS_REGION}"
                    ]) {
                        stage("Validate AWS credentials") {
                            sh "aws sts get-caller-identity"
                        }

                        stage ("Cloudformation Deploy") {
                            sh """
                                aws cloudformation deploy \
                                    --template-file infra/ecs.yml \
                                    --stack-name ${AWS_CLOUDFORMATION_STACK_NAME} \
                                    --capabilities CAPABILITY_NAMED_IAM \
                                    --parameter-overrides \
                                    ImageUrl=${DOCKER_IMAGE_REPOSITORY_NAME}:${DOCKER_IMAGE_TAG} \
                                    VpcId=${AWS_VPC_ID} \
                                    Subnets="${AWS_SUBNETS}"
                            """
                        }
                    }
                }
            }
        }
    } catch (Exception e) {
        echo "Pipeline failed: ${e.message}"
        throw e
    } finally {
        echo "Pipeline finished."
    }
}