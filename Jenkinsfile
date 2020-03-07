pipeline {
    agent any
    stages {
        stage('Stage 1') {
            agent {
              kubernetes {
                label 'kaniko'
                idleMinutes 5
                yamlFile 'build-pod.yaml'
                defaultContainer 'kaniko'
              }
            }
            steps {
              sh "/kaniko/executor \
                    --context=git://github.com/rkamradt/hellok8s.git#refs/heads/master \
                    --dockerfile=DockerfileHelloworld \
                    --verbosity=debug \
                    --cache=true \
                    --insecure=true \
                    --destination=registry.container-registry:5000/helloworld:latest"
            }
        }
    }
}
