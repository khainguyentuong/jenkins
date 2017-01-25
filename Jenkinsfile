node {
    try {    
        stage('checkout') {
            git 'https://github.com/spring-projects/spring-boot.git'
        }
        
        
        dir('spring-boot-samples/spring-boot-sample-atmosphere') {
            stage('build') {
                sh 'mvn clean package'
            }    

            stage('sonarqube') {
                tool name: 'SonarQubeScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            }
            
            stage('package') {
                archiveArtifacts artifacts: 
                    'target/*.jar', 
                    onlyIfSuccessful: true
            }
        }
    }
    catch (err) {
        notify("Error ${err}")
        currentBuild.result = 'FAILURE'
    }
}

def notify(message) {
    emailext(
        to: 'khainguyentuong@gmail.com',
        subject: "${message}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: "<a href='${env.BUILD_URL}'>${env.JOB_NAME}</a>"
    )
}  