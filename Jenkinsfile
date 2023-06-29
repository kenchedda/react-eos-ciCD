  def label = "deploy"
  podTemplate(label: label, yaml: """
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: deploy
  spec:
    serviceAccount: jenkins-agent-sa
    containers:
    - name: deploy
      image: kenappiah/agentbuild:4
      command:
      - cat
      tty: true
  """
  ) {
      node (label) {

          stage ('Checkout SCM'){
            git url: 'https://github.com/kenchedda/react-eos-ciCD.git', branch: 'dev'
          }

          stage ('Helm Chart') {
            container('deploy') {
              dir('charts') {
                withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add ken-helm-helm-local "https://kenappiah.jfrog.io/artifactory/ken-helm-helm-local/" --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm install eos-webapp-dev --namespace dev -f values.yaml ."
                      sh "/usr/local/bin/helm list -a --namespace dev"
                }
              }
          }
          }
      }
  }