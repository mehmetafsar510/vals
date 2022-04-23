pipeline {
    agent {slave}
    environment {
        PATH=sh(script:"echo $PATH:/usr/local/bin", returnStdout:true).trim()
        GIT_FOLDER = sh(script:'echo ${GIT_URL} | sed "s/.*\\///;s/.git$//"', returnStdout:true).trim()
    }
    stages{
        stage('Test') {
            withCredentials([
                string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')
            ]) {
               script {

                env.VAULT_IP = sh(script:"curl http://169.254.169.254/latest/meta-data/public-ipv4", returnStdout:true).trim()
              }
              sh """
              export VAULT_TOKEN=${env.VAULT_TOKEN}
              export VAULT_ADDR=https://${env.VAULT_IP}:8200
              export VAULT_CACERT=${env.VAULT_CACERT}
              echo "password: ref+vault://mehmet/crediantial#/password" | vals eval -f -
              """
                }         
            
        }
    
    }
}
