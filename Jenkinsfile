pipeline{
    agent{
        label 'k8s-node'
    }
    environment{
       APPLICATION_NAME= 'eureka'
       POM_VERSION= readMavenPom().getVersion();
       POM_PACKAGING= readMavenPom().getPackaging();

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
                  echo " actual artifact: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}-${env.POM_PACKAGING}"
                }
            }
        }
    }
