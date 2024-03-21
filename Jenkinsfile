pipeline{
    agent{
        label 'k8s-node'
    }
    environment{
       APPLICATION_NAME= eureka
       POM_VERSION= readMavenPom().getVersion()

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
                    sh "cat /home/raksharoshni/jenkins/workspace/1project_master/target/*.jar"
                }
            }
        }
    }
