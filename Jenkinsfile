pipline {
  agent any
  stages {
    stage('Build'){
      steps {
        echo 'Running Build automatinon'
        sh './gradlew build --no-daemon'
        archiveArtifacts artifacts: 'dist/trainSchedule.zip'
      }
    }
    stage('DeployToStaging'){
      when{
        branch 'master'
      }
      step {
        withCredentials([usernamePassword(credentialsId: 'staging_user', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
          sshPublisher(
            failOnError: true,
            continueOnError: false,
            publishers: [
              sshPublisherDesc(
                configName: 'staging',
                sshCredentials: [
                  username: "$USERNAME",
                  encryptedPassphrase: "$USERPASS"
                  ],
                transfer: [
                  sshTransfer{
                    sourceFile: 'dist/trainSchedule.zip',
                    removePrefix: 'dist/',
                    remoteDirectory: '/tmp'
                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                  }
                ]
              )
            ]
          )
        }
      }
    }
    stage('DeployToProd'){
      when{
        branch 'master'
      }
      step {
        input 'Does the staging environment look OK?'
        milestone(1)
        withCredentials([usernamePassword(credentialsId: 'staging_user', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
          sshPublisher(
            failOnError: true,
            continueOnError: false,
            publishers: [
              sshPublisherDesc(
                configName: 'staging',
                sshCredentials: [
                  username: "$USERNAME",
                  encryptedPassphrase: "$USERPASS"
                  ],
                transfer: [
                  sshTransfer{
                    sourceFile: 'dist/trainSchedule.zip',
                    removePrefix: 'dist/',
                    remoteDirectory: '/tmp'
                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                  }
                ]
              )
            ]
          )
        }
      }
    }
