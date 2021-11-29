pipeline {
  agent {
      kubernetes {
      label 'kubernetes'
      defaultContainer 'podman'
     yaml '''
---
apiVersion: v1
kind: Pod
metadata:
  namespace: "jenkins"
spec:
  containers:
    - name: podman
      imagePullPolicy: Always
      image: docker.io/mysticrenji/podman:latest
      command:
        - cat
      tty: true
      securityContext:
        privileged: true
      volumeMounts:
        - mountPath: /var/lib/containers
          name: podman-volume
        - mountPath: /dev/shm
          name: devshm-volume
        - mountPath: /var/run
          name: varrun-volume
        - mountPath: /tmp
          name: tmp-volume
  restartPolicy: Never
  volumes:
    - name: podman-volume
      emptyDir: {}
    - emptyDir:
        medium: Memory
      name: devshm-volume
    - emptyDir: {}
      name: varrun-volume
    - emptyDir: {}
      name: tmp-volume
      '''
      }}
   stages {
      stage('Podman Build') {
         steps {
            container('podman') {
              sh '''
              echo -e 'FROM busybox\nRUN echo "hello world"' | podman --events-backend=file build -t  ghcr.io/mysticrenji/hello:latest -
              '''
            }
         }
      }
      stage('Podman docker-compose') {
         steps {
            container('podman') {
              sh '''
              git clone https://github.com/mysticrenji/podman-experiments.git
              cd podman-experiments
              podman-compose up -d
              podman-compose down
              podman images
              '''
            }
         }
      }
   }
}
