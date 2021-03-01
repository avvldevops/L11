pipeline {

  agent {

    docker {
      label 'docker'
      image '130.193.55.54:8123/mydocker/mydocker:latest'
      args  '-v /var/run/docker.sock:/var/run/docker.sock -u 0:0'
      registryUrl 'http://130.193.55.54:8123/mydocker'
      registryCredentialsId '822542a2-b541-3eaa-4451-2f31ea836910'
    }
  }

  stages {

    stage('Clone the app from github') {
      steps{
        git 'https://github.com/avvldevops/boxfuse1'
      }
    }

    stage('Build the app') {
      steps {
        sh 'mvn package'
      }
    }

    stage('Prepare docker image and push it into private registry') {
      steps {
        sh 'docker build -f Dockerfile -t http://130.193.55.54:8123/mydocker/myweb:latest .'
	sh 'cat /home/docker/my_password.txt | docker login 130.193.55.54:8123  --username admin --password-stdin'
        sh 'docker push http://130.193.55.54:8123/mydocker/myweb:latest'
      }
    }

    stage('Pull docker image from private registry and run it') {
      steps {
          sshagent(['da73903a-a142-32c0-120ba-1c13412812fa']) {
          sh '''ssh -o StrictHostKeyChecking=no root@130.193.55.32 << EOF
          sh cat /home/docker/my_password.txt | docker login 130.193.55.54:8123  --username admin --password-stdin
          docker pull http://130.193.55.54:8123/mydocker/myweb:latest
          docker run --rm --name myapp -d -p 8080:8080 http://130.193.55.54:8123/mydocker/myweb:latest
EOF'''
        }
      }
    }
  }
}