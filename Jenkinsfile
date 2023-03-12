node {
   def registryProjet='registry.gitlab.com/myfistgroup2/cmpilationwar'
   def IMAGE="${registryProjet}:version-${env.BUILD_ID}"
    stage('Build - Clone') {
           git credentialsId: 'reg2', url: 'https://github.com/theprinceblacky/jenkins-ansible-docker.git'
    }
    stage('Build - Maven package'){
            sh 'mvn package'
    }
    def img = stage('Build') {
          docker.build("$IMAGE",  '.')
    }
    stage('Build - Test') {
            img.withRun("--name run-$BUILD_ID -p 8081:8080") { c ->
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 30s'
            sh 'curl 127.0.0.1:8081'
            sh 'docker ps'
          }
    }
    stage('Build - Push') {
          docker.withRegistry('https://registry.gitlab.com', 'reg1') {
              img.push 'latest'
              img.push()
          }
    }
    stage('Deploy - Clone') {
          git credentialsId: 'reg2', url: 'https://github.com/theprinceblacky/jenkins-ansible-docker.git'
    }
    stage('Deploy - End') {
      ansiblePlaybook (
          colorized: true,
          become: true,
          playbook: 'playbook.yml',
          inventory: '${HOST},',
          extras: "--extra-vars 'image=$IMAGE'"
      )
    }

}

