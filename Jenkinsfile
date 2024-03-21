pipeline{
    agent{
        label 'k8s-node'
    }
    environment{
       APPLICATION_NAME= 'eureka'
       POM_VERSION= readMavenPom().getVersion();
       POM_PACKAGING= readMavenPom().getPackaging();
       DOCKER_CREDS = credentials('dockerhub')

    }
    tools{
        maven 'maven'
        jdk 'java'
    }
        stages{
            stage(maven){
                steps{
                    script{
                        echo "hello"
                        sh "mvn package"
                    }
                }
              post{
                always{
                    junit 'target/surefire-reports/*.xml'
                }
              }
            }
            stage(artifactorycheck){
                steps{
                    sh "ls /home/raksharoshni/jenkins/workspace/1project_master/target/*.jar"
                }
            }
            stage(check){
                steps{
                  echo " actual artifact: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                }
            }
            stage(Dockerpush){
                steps{
                   sh """
                     ls -la
                     cp ${workspace}/target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} ./.cicd
                     ls -la ./.cicd
                     docker build --build-args JAR_SRC= i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} -t ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT} ./.cicd
                     docker images
                     docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}
                     docker push ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}

                     """ 
                }
            }
        }
    }
