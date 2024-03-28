pipeline{
    agent{
        label 'k8s-node'
    }
    parameters{
        choice(name:'mavenBuild', choices: ['no','yes'],description:'This will build')
        choice(name:'artifactoryCheck', choices: ['no','yes'],description:'This will check the artifactory')
        choice(name:'dockerPush', choices: ['no','yes'],description:'This will push the artifactory to dockerhub')
        choice(name:'dev', choices: ['no','yes'],description:'This will deploy app to dev')
        choice(name:'stage', choices: ['no','yes'],description:'This will deploy app to stage')
        choice(name:'test', choices: ['no','yes'],description:'This will deploy app to test')
        choice(name:'prod', choices: ['no','yes'],description:'This will deploy app to prod')
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
            stage('maven-build'){
            when{
                anyOf{
                 expression {

                    params.mavenBuild=='yes'
                    params.dockerPush=='yes'
                    params.dev=='yes'
                    params.stage=='yes'
                    params.test=='yes'
                    params.prod=='yes'
                 }
                }
            }
                steps{
                    script{
                        echo "Building the application"
                        sh "mvn package"
                    }
                }
              post{
                always{
                    junit 'target/surefire-reports/*.xml'
                }
              }
            }
            stage('artifactorycheck'){
                when{
                    expression{
                    params.artifactoryCheck=='yes'
                    }
                }
                steps{
                    sh "ls /home/raksharoshni/jenkins/workspace/1project_master/target/*.jar"
                }
            }
            stage('check'){
                steps{
                  echo " actual artifact: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                }
            }
            stage('Dockerpush'){
                when{
                    expression{
                    params.mavenBuild=='yes'
                    params.dockerPush=='yes'
                    params.dev=='yes'
                    params.stage=='yes'
                    params.test=='yes'
                    params.prod=='yes'
                    }
                }
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
            stage('dev'){
             when{
                expression{
                params.dev=='yes'
                }
             }
             steps {
                script{
                    dockerDeploy('dev', '5761', '8761').call()
                    echo "deployed successfull in dev"
                }
             }
            }
            stage('Test'){
            when{
                expression{
                params.test=='yes'
                }
             }
             steps {
                script{
                    dockerDeploy('Test', '6761', '8761').call()
                    echo "deployed successfull in Test"
                }
             }
            }
            stage('Stage'){
             when{
                expression{                    
                params.stage=='yes'
                }
             }
             steps {
                script{
                    dockerDeploy('Stage', '7761', '8761').call()
                    echo "deployed successfull in Stage"
                }
             }
            }
            stage('Prod'){
            when{
                expression{
                params.prod=='yes'
                }
             }
             steps {
                script{
                    dockerDeploy('Prod', '8761', '8761').call()
                    echo "deployed successfull in Prod"
                }
             }
            }
            stage('clean'){
                steps{
                    cleanWs()
                }
            }
     }
}

 def dockerDeploy(envDeploy, hostPort, contPort) {
    return {
    echo "******************************** Deploying to $envDeploy Environment ********************************"
    withCredentials([usernamePassword(credentialsId: 'docker_vm_maha_user', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
        // some block
        // With the help of this block, ,the slave will be connecting to docker-vm and execute the commands to create the containers.
        //sshpass -p ssh -o StrictHostKeyChecking=no user@host command_to_run
        //sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} hostname -i" 
        
    script {
        // Pull the image on the Docker Server
        sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker pull ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}"
        
        try {
            // Stop the Container
            echo "Stoping the Container"
            sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker stop ${env.APPLICATION_NAME}-$envDeploy"

            // Remove the Container 
            echo "Removing the Container"
            sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker rm ${env.APPLICATION_NAME}-$envDeploy"
             } catch(err) {
            echo "Caught the Error: $err"
        }

        // Create a Container 
        echo "Creating the Container"
        sh "sshpass -p ${PASSWORD} -v ssh -o StrictHostKeyChecking=no ${USERNAME}@${docker_vm_ip} docker run -d -p $hostPort:$contPort --name ${env.APPLICATION_NAME}-$envDeploy ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${GIT_COMMIT}"
        }
    }
    }
    
}


// cp /home/i27k8s10/jenkins/workspace/i27-Eureka_master/target/i27-eureka-0.0.1-SNAPSHOT.jar ./.cicd

// workspace/target/i27-eureka-0.0.1-SNAPSHOT-jar

// i27devopsb2/eureka:tag


// Eureka container runs at 8761 port 
// I will configure env's in a way they will have diff host ports
// dev ==> 5761 (HP)
// test ==> 6761 (HP)
// stage ==> 7761 (HP)
// Prod ==> 8761 (HP)


// stop ==> remove 

// run

// cp /home/i27k8s10/jenkins/workspace/i27-Eureka_master/target/i27-eureka-0.0.1-SNAPSHOT.jar ./.cicd

// workspace/target/i27-eureka-0.0.1-SNAPSHOT-jar

// i27devopsb2/eureka:tag


// Eureka container runs at 8761 port 
// I will configure env's in a way they will have diff host ports
// dev ==> 5761 (HP)
// test ==> 6761 (HP)
// stage ==> 7761 (HP)
// Prod ==> 8761 (HP)


// stop ==> remove 

// run