// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: longwuyuan/podman
    command:
    - sleep
    args:
    - infinity
'''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            defaultContainer 'shell'
        }
    }
    stages {
        stage('Main') {
            steps {
                sh 'hostname && sysctl -A | grep -i namespace && ls -l /'
                git 'https://github.com/longwuyuan/jenkins-kubernetes-podman.git'
                sh 'echo "Testing if podman build works"'
                sh 'podman --storage-driver vfs --runroot /home/jenkins/ --root /home/jenkins images '
                sh 'whoami && pwd && ls -alth && echo "WORKSPACE=$WORKSPACE"'
                sh 'podman --storage-driver vfs --runroot /home/jenkins/ --root /home/jenkins build -t test-podman-build -f Dockerfile.testbuild .'
                sh 'podman --storage-driver vfs --runroot /home/jenkins/ --root /home/jenkins images '
                sh 'podman --storage-driver vfs --runroot /home/jenkins/ --root /home/jenkins -pid=host build -t jenkins-kubernetes-podman .'
                sh 'podman --storage-driver vfs --runroot /home/jenkins/ --root /home/jenkins images '
            }
        }
    }
}
