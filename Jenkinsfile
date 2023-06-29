pipeline{
// IMAGE_VERSION_PLACEHOLDER="replaceImageTag"
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
        //         SONAR_URL = "https://d327-2405-201-a405-1893-1cc7-acf6-c642-a8a6.ngrok-free.app"
        //         }

        //     steps{
        //         echo "========Start Testing========"
                
        //         withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
        //             // sh 'sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        //             sh 'sonar-scanner -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
                    
        //         }
        //     }
        // }

         stage("Build docker file and push"){

            environment {
                DOCKER_IMAGE = "sushank3/ci_cd-reddit:v${BUILD_NUMBER}"
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

        stage('Update Deployment File') {

            environment {
                GIT_REPO_NAME = "CI_CD-reddit"
                GIT_USER_NAME = "sushank3"
            }
        
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                    sh '''
                        git config user.email "sushankkr3@gmail.com"
                        git config user.name "Sushank Kumar"
                        
                        BUILD_NUMBER="${BUILD_NUMBER}"

                        CURRENT_IMAGE_VERSION=$(grep -oE 'sushank3/ci_cd-reddit:v[0-9]+' manifest/deployment.yaml | cut -d':' -f2)

                        

                        sed -i "s/${CURRENT_IMAGE_VERSION}/${BUILD_NUMBER}/g" manifest/deployment.yaml
                        
                    
                        git add manifest/deployment.yaml
                        
                        git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                        
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                       
                    '''
                }
            }
        }
    }
    
}