pipeline{

    agent any
    // agent {
    //     docker {
    //         image 'sushank3/environment:v11'
    //         args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    //     }
    // }

    stages{

        // stage('Build and Test') {
        //     steps {
        //         sh 'ls -ltr'
        //         // build the project and create a JAR file
        //         sh 'npm pack'
        //         }
                
        //     }

        // stage("Static Code Analysis"){

        //     environment {
        //         SONAR_URL = "https://b6c3-150-242-72-128.ngrok-free.app"
        //         }

        //     steps{
        //         echo "========Start Testing========"
                
        //         withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
        //             sh 'mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        //         }
        //     }
        // }

         stage("Build docker file and push"){

            environment {
                DOCKER_IMAGE = "sushank3/CI_CD-reddit:${BUILD_NUMBER}"
                // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
                REGISTRY_CREDENTIALS = credentials('docker_hub')
            
            }

            steps{

                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry("https://index.docker.io/v1/", "docker_hub") {
                        dockerImage.push()
                    }
                }
            }
        }
    }
    
}