podTemplate(yaml: '''
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:v1.6.0-debug
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - 99d
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: registry-credentials
          items:
            - key: .dockerconfigjson
              path: config.json
''') {

  node(POD_LABEL) {

    stage('Build with Kaniko') {
      git branch: 'main', url: 'https://github.com/chanwit/hello-world'
      def tag = sh(returnStdout: true, script: "git tag --contains").trim()
      container('kaniko') {
        if(tag) {
            sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --destination=docker.io/chanwit/dev-demo:' + tag
        } else {
            sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --destination=docker.io/chanwit/dev-demo:latest'
        }
      }
    }

  }

}