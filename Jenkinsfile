pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools{
    maven 'my_maven'
  }

  environment  {
    gitName = 'dduddu00'
    gitEmail = 'tndusdlsms@nate.com'
    gitWebaddress = 'https://github.com/dduddu00/sb2_code.git'
    gitSshaddress = 'git@github.com:dduddu00/sb2_code.git'
    gitCredential = 'git_cre' //git Credential 생성시의 ID
    dockerHubRegistry = 'choisooyeon/sbimage' 
    dockerHubRegistry2 = 'choisooyeon/supermario'
    dockerHubRegistryCredential = 'docker_cre' // dcker Credential 생성시의 ID
  }

  stages {
    stage('Checkout Github') {
        steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: gitCredential, url: gitWebaddress]]])
            // 코드 빌드 시 어디에서 받아올 것인지 정함
        }
        post {
            //성공 또는 실패했을 경우 명령이 출력되도록 만들기
            failure {
                echo 'Repository Clone Failure'
            }
            success {
                echo 'Repository Clone Success'
            }
        }
    }
    stage('maven Build') {
        steps {
            sh 'mvn clean install'
            //maven 플러그인이 미리 설치되어 있어야 한다.
        }
        post {
            failure {
                echo 'maven build failure'
            }
            success {
                echo 'maven build success'
            }
        }
    }
    stage('Docker image Build') {
        steps {
            sh "docker build -t ${dockerHubRegistry}:${currentBuild.number} ."
            sh "docker build -t ${dockerHubRegistry}:latest ."
            sh "docker build -t ${dockerHubRegistry2}:${currentBuild.number} -f Dockerfile2 ."
            sh "docker build -t ${dockerHubRegistry2}:latest -f Dockerfile2 ."
        }
        post {
            failure {
                echo 'docker image build failure'
            }
            success {
                echo 'docker image build success'
            }
        }
    }
    stage('Docker image push') {
        steps {
            withDockerRegistry(credentialsId: dockerHubRegistryCredential, url: '') {
            sh "docker push ${dockerHubRegistry}:${currentBuild.number}"
            sh "docker push ${dockerHubRegistry}:latest"
            sh "docker push ${dockerHubRegistry2}:${currentBuild.number}"
            sh "docker push ${dockerHubRegistry2}:latest"
          }

        }
        post {
            failure {
                echo 'docker image build failure'
                sh "docker image rm -f ${dockerHubRegistry}:${currentBuild.number}"
                sh "docker image rm -f ${dockerHubRegistry}:latest"
            }
            success {
                echo 'docker image build success'
                sh "docker image rm -f ${dockerHubRegistry}:${currentBuild.number}"
                sh "docker image rm -f ${dockerHubRegistry}:latest"
            }
        }
    }
   stage('k8s manifest file update') {
      steps {
        git credentialsId: gitCredential,
            url: gitWebaddress,
            branch: 'main'
        
        // 이미지 태그 변경 후 메인 브랜치에 푸시
        sh "git config --global user.email ${gitEmail}"
        sh "git config --global user.name ${gitName}"
        sh "sed -i 's@${dockerHubRegistry}:.*@${dockerHubRegistry}:${currentBuild.number}@g' deploy/sb-deploy.yml"
        sh "sed -i 's@${dockerHubRegistry2}:.*@${dockerHubRegistry2}:${currentBuild.number}@g' deploy/mario.yml"
        sh "git add ."
        sh "git commit -m 'fix:${dockerHubRegistry} & ${dockerHubRegistry2} ${currentBuild.number} image versioning'"
        sh "git branch -M main"
        sh "git remote remove origin"
        sh "git remote add origin ${gitSshaddress}"
        sh "git push -u origin main"

      }
      post {
        failure {
          echo 'Container Deploy failure'
        }
        success {
          echo 'Container Deploy success'  
        }
      }
    }
  }
}
