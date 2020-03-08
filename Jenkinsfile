pipeline {
    agent any
    stages {
      stage('Build Image') {
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
      stage('Deploy Stage') {
          agent {
            kubernetes {
              label 'kubectl'
              idleMinutes 5
              yamlFile 'kubectl-pod.yaml'
              defaultContainer 'kubectl'
            }
          }
          steps {
            sh "/usr/local/bin/kubectl apply -f helloworld.yaml"
          }
      }
      stage('Build Image') {
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
                  --dockerfile=DockerfileHelloworldTest \
                  --verbosity=debug \
                  --cache=true \
                  --insecure=true \
                  --destination=registry.container-registry:5000/helloworldtest:latest"
          }
      }
      stage('Deploy Stage') {
          agent {
            kubernetes {
              label 'kubectl'
              idleMinutes 5
              yamlFile 'kubectl-pod.yaml'
              defaultContainer 'kubectl'
            }
          }
          steps {
            sh "/usr/local/bin/kubectl apply -f helloworldtest.yaml"
          }
      }
    }
}
