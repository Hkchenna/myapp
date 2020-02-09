node {
    stage('SourceCodeFromGIT') {
        git credentialsId: 'GitHub', poll: false, url: 'https://github.com/suresh-devops/myapp.git'
    }
    VERSION = sh (
      script: 'sh scripts/project_version.sh',
      returnStdout: true
      ).trim()
    stage('MavenBuildWithOutTests') {
      sh label: '', script: 'mvn clean package -DskipTests'
    }
    stage('MavenBuildWithOutTests') {
      sh label: '', script: 'mvn clean package'
    }
    stage('DockerBuild') {
      withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USERNAME')]) {
        sh label: '', script: "docker build -t ${DOCKER_USERNAME}/my-app:${VERSION} ."
      }
    }
    stage('DockerLogin') {
      withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USERNAME')]) {
        sh label: '', script: "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASS}"
      }
    }
    stage('DockerPushToDockerHub') {
      withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USERNAME')]) {
        sh label: '', script: "docker push ${DOCKER_USERNAME}/my-app:${VERSION}"
      }
    }
    stage('DeployOnDevSever') {
    withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USERNAME')]) {
      sshagent(['ec2-user']) {
          def CONTIONER1 = "docker run -d --name tomcat1 -p 8081:8080 ${DOCKER_USERNAME}/my-app:${VERSION}"
          def CONTIONER2 = "docker run -d --name tomcat2 -p 8082:8080 ${DOCKER_USERNAME}/my-app:${VERSION}"
          def CONTIONER3 = "docker run -d --name tomcat3 -p 8083:8080 ${DOCKER_USERNAME}/my-app:${VERSION}"
          def CONTIONER4 = "docker run -d --name tomcat4 -p 8084:8080 ${DOCKER_USERNAME}/my-app:${VERSION}"
          sh label: '', script: "ssh -o StrictHostKeyChecking=no ec2-user@34.230.70.85 ${CONTIONER1}"
          sh label: '', script: "ssh -o StrictHostKeyChecking=no ec2-user@34.230.70.85 ${CONTIONER2}"
          sh label: '', script: "ssh -o StrictHostKeyChecking=no ec2-user@34.230.70.85 ${CONTIONER3}"
          sh label: '', script: "ssh -o StrictHostKeyChecking=no ec2-user@34.230.70.85 ${CONTIONER4}"
      }
    }
  }
}
