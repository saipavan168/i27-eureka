pipeline{
    agent{
        label 'k8s-node'
    }
    {
        stages{
            stage(maven){
                steps{
                    script{
                        echo "hello"
                    }
                }
            }
        }
    }
}