pipeline {

  agent any 
  
   environment{
     TLS_JAVA = 'JAVA8'
     TLS_MAVEN = 'MAVEN3'
  }

  

  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    disableConcurrentBuilds()
    timeout(time: 60, unit: 'MINUTES')
  }

  triggers {
    pollSCM('H */15 * * 1-5')
    upstream(upstreamProjects: "trx-store/develop,trx-store/develop30,trx-store/master",
             threshold: hudson.model.Result.SUCCESS)
  }

  stages {
    stage('Checkout SCM') {
      steps {
        checkout scm
      }
    }

    stage('Prepare JBoss configuration') {
      steps {
	  withMaven(maven: env.TLS_MAVEN, jdk: env.TLS_JAVA) {
        
          sh '''
		    cd my-app
            mvn package
            java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App
          '''
        }
      }
    }

    /*stage('Tag Artifact') {
      when { branch 'master' }

      steps {
        script {
          def pom = readMavenPom();
          pom.version = pom.version.replaceAll("\\d+\\-SNAPSHOT", env.BUILD_NUMBER + "-RELEASE")
          writeMavenPom(model: pom)

          env.VERSION = pom.version
          env.GIT_URL = scm.getUserRemoteConfigs()[0].getUrl()

          withCredentials(bindings: [usernamePassword(credentialsId: env.GIT_CREDENTIALS, passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            sh '''
              GIT_URL=https://${GIT_USERNAME}:${GIT_PASSWORD}@${GIT_URL:8}
              git config user.name build_agent
              git config user.email noreply@fmr.com
              git commit -a -m "[RELEASE] Updating pom.xml with version $VERSION"
              git tag -a $VERSION -m "[RELEASE] Creating tag for release version $VERSION"
              git push $GIT_URL --tags
            '''
          }
        }
      }
    }*/


   
    /*failure {
      script {
        def body = """
          <p>Job '<b>${env.JOB_NAME} [${env.BUILD_NUMBER}]</b>' build failure.</p>
          <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>
        """
        def subject = "FAILURE: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
        emailext(attachLog: true, from: 'noreply@jenkins.fmr.com', mimeType: 'text/html', replyTo: 'noreply@jenkins.fmr.com', recipientProviders: [culprits(), developers()], subject: subject, body: body)
      }
    }*/
    /*fixed {
      script {
        def body = """
          <p>Job '<b>${env.JOB_NAME} [${env.BUILD_NUMBER}]</b>' has now been fixed.</p>
          <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>
        """
        def subject = "FIXED: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
        emailext(attachLog: true, from: 'noreply@jenkins.fmr.com', mimeType: 'text/html', replyTo: 'noreply@jenkins.fmr.com', recipientProviders: [culprits(), developers()], subject: subject, body: body)
      }
    }*/
  }

}
