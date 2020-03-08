pipeline {
    agent any
    stages {
      stage('Build Image') {
          agent {
            kubernetes {
              label 'kaniko'
              idleMinutes 1
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
            sh "/usr/local/bin/kubectl apply -f helloworld.yaml; sleep 120"
          }
      }
      stage('Build Test Image') {
          agent {
            kubernetes {
              label 'kaniko'
              idleMinutes 1
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
      stage('Run Test') {
          agent {
            kubernetes {
              label 'kubectl'
              idleMinutes 5
              yamlFile 'kubectl-pod.yaml'
              defaultContainer 'kubectl'
            }
          }
          steps {
            sh "/usr/local/bin/kubectl delete -f helloworldtest.yaml"
            sh "/usr/local/bin/kubectl apply -f helloworldtest.yaml"
            sh "/usr/local/bin/kubectl wait --for=condition=complete --timeout=600s job/helloworldtest"
          }
      }
    }
    post {
        always {
            emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"

        }
    }
}
