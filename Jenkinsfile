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
    }
}
