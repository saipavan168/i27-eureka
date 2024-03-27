pipeline{
    agent{
        label 'k8s-node'
    }
    
    environment{
       APPLICATION_NAME= 'eureka'
       POM_VERSION= readMavenPom().getVersion()
       POM_PACKAGING= readMavenPom().getPackaging()
       DOCKER_CREDS = credentials('dockerhub')
       DOCKER_HUB= "docker.io/raksharoshni"

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
                     docker build --force-rm --no-cache --pull --rm=true --build-arg JAR_SRC=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} -t ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT} ./.cicd
                     docker images
                     docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}
                     docker push ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}

                     """ 
                }
            }

            stage(deploy){
                echo "************** Deploying to Dev***************"
                withCredentials([usernamePassword(credentialsId: 'docker_vm_maha_user', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                                        // some block
                script{  
                    sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker pull ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}"

                    try{
                        //stop
                            sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker stop ${env.APPLICATION_NAME}-$env"
                        //remove
                            sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker rm ${env.APPLICATION_NAME}-$env"
                    }
                    catch(err){
                        echo " caught the err: $err"
                    }
                    //Below loc is to login and run the image 
                        sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker run -d -p $hostip:8761 --name ${env.APPLICATION_NAME}-$env ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}"
                        //Below loc is to run the jar file
                    }
                 }
            }
     }
}
 /*   
            stage(Deploy_to_dev){
                steps{
                   script{
                    deploying('dev','5761').call()
                   }
                }
            }
            stage(Deploy_to_Tst){
                steps{
                  script{
                   deploying('tst','6761').call()
                  }
                }
            }
            stage(Deploy_to_Prod){
                steps{
                  script{
                   deploying('tst','7761').call()
                  }
                }
            }            
        }


} */
/*
def deploying(env,hostip){
    return{
     echo "************** Deploying to Dev***************"
     withCredentials([usernamePassword(credentialsId: 'docker_vm_maha_user', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                            // some block
      script{  
         sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker pull ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}"

        try{
             //stop
                sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker stop ${env.APPLICATION_NAME}-$env"
            //remove
                 sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker rm ${env.APPLICATION_NAME}-$env"
          }
        catch(err){
             echo " caught the err: $err"
          }
         //Below loc is to login and run the image 
             sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker run -d -p $hostip:8761 --name ${env.APPLICATION_NAME}-$env ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}"
             //Below loc is to run the jar file
           }
         }
    }
}
*/