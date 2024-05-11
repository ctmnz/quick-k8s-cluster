helm install my-jenkins -f jenkins.vars  oci://registry-1.docker.io/bitnamicharts/jenkins

kubectl get secret --namespace default my-jenkins -o jsonpath="{.data.jenkins-password}" | base64 -d


---
Cloud -> Kubernetes

- [V] WebSocket

- Jenkins URL: http://my-jenkins.default.svc.cluster.local:80


---

```
podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
      - name: bash
        image: bash:latest
        command:
        - sleep
        args:
        - 99d
      - name: golang
        image: golang:1.16.5
        command:
        - sleep
        args:
        - 99d
      - name: groovy
        image: groovy:4.0
        command:
        - sleep
        args:
        - 99d
      - name: busybox
        image: busybox
        command:
        - sleep
        args:
        - 99d
      - name: docker-client
        image: docker:latest
        command: ['sleep', '99d']
        env:
          - name: DOCKER_HOST
            value: tcp://localhost:2375
      - name: docker-daemon
        image: docker:dind
        env:
          - name: DOCKER_TLS_CERTDIR
            value: ""
        securityContext:
          privileged: true
        volumeMounts:
            - name: cache
              mountPath: /var/lib/docker
      volumes:
        - name: cache
          hostPath:
            path: /tmp
            type: Directory          
          
          
''') {
  node(POD_LABEL) {
    stage('Get a Maven project') {
      container('maven') {
        stage('Build a Maven project') {
          sh 'mvn --version'
        }
      }
    }

    stage('Get a Golang project') {
      container('golang') {
        stage('Build a Go project') {
          sh '''
          go version
          go version
          go version
          '''
        }
      }
    }
    
    stage('Mixed container stage') {
        container('groovy') {

            sh """
#!/bin/bash
cat > Dockerfile <<- EOT
FROM busybox
RUN mkdir -p /opt/out
RUN echo "Hello there! Bruh!" > /opt/out/hello.txt
RUN echo "All good!" >> /opt/out/hello.txt
EOT
"""
        }
    }
    
    stage('Reading the Dockerfile') {

        container('groovy') {
                sh "echo 'All good!' >> hello.txt"

        }
                
        container('bash') {
            sh "cat hello.txt"
            sh "cat Dockerfile"
            
            retry(10) {
                sh "sleep 1"
                sh 'timeout 10 bash -c "</dev/tcp/localhost/2375"'
            }
        }
        
        container('docker-client') {
            sh 'docker version && DOCKER_BUILDKIT=1 docker build --progress plain -t testing .'
        }
    }

  }
}
```
