node {
    def mavenHome = "/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/maven_3.6.3"

    stage('Checkout Code') {
        git branch: 'development', credentialsId: '0e215e3b-6d89-4c1d-a5f1-92a613f44f72', url: 'https://github.com/krishnavamsi6281/maven-web-application.git'
    }

    stage('Build') {
        // Clean and build the application
        sh "${mavenHome}/bin/mvn clean package"

        // Verify if WAR file is created
        sh "ls -l target/"

        // Ensure the WAR file exists
        if (!fileExists('target/maven-web-application.war')) {
            error "WAR file not found! Build failed."
        }
    }
    
    stage('Execute SonarQube Report') {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }

    stage('Upload Artifacts into Nexus') {
        sh "${mavenHome}/bin/mvn deploy"
    }

    stage('Deploy App Into Tomcat') {
        sshagent(['27bf3cc8-e354-4497-9789-848cdb28ba0f']) {
            sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.221.226.75:/opt/apache-tomcat-9.0.102/webapps"
        }
    }
    
  stage('Send EmailNotification')
  {
      emailext body: '''Build Over

      Regards,
     Vamsi''', subject: 'Build Over', to: 'vmsh333@gmail.com'
  }
}
