pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        
        stage('DeployToStaging'){
            when {
            branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'merygit',usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                     publishers: [
                         configName: 'stagingMery
                         sshCredentials: [
                               userName: "$USERNAME",
                               encryptedPassPhrase: "$USERPASS"
                         ],
                         transfers: [
                            sshTransfer(
                                sourceFiles: 'dist/trainSchedule.zip',
                                removePrefix: 'dist/',
                                remoteDirectory: '/tmp',
                                execCommand: 'sudo /usr/bin/systectl stop train-schedule && rm -rf /opt/train-schedule/* unzip /tmp/train-schedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                            )
                         ]                    ]
                    )
                ]
               )
            }
                
        }
   }
        stage('DeployToProduction'){
            when {branch 'master'}
            
            steps{
            input 'Does the staging Environment look ok?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'merygit',usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                    sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                     publishers: [
                         configName: 'productionMery
                         sshCredentials: [
                               userName: "$USERNAME",
                               encryptedPassPhrase: "$USERPASS"
                         ],
                         transfers: [
                            sshTransfer(
                                sourceFiles: 'dist/trainSchedule.zip',
                                removePrefix: 'dist/',
                                remoteDirectory: '/tmp',
                                execCommand: 'sudo /usr/bin/systectl stop train-schedule && rm -rf /opt/train-schedule/* unzip /tmp/train-schedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                            )
                         ]                    ]
                    )
                ]
               )
            }
                
        }
   }
