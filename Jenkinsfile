pipeline {
  agent any
 
  tools {
  maven 'Maven3'
  }
  stages {
    stage ('Build') {
      steps {
      sh 'mvn clean install -f MyWebApp/pom.xml'
      }
    }
    stage ('Code Quality') {
      steps {
        withSonarQubeEnv('SonarQube') {
        sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
        }
      }
    }
    stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }
    stage ('Nexus Upload') {
      steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: 'http://ec2-18-207-139-110.compute-1.amazonaws.com:8081',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'c531deff-0431-4355-8e73-ee5dc27d53c7',
      artifacts: [
      [artifactId: 'MyWebApp',
      classifier: '',
      file: 'MyWebApp/target/MyWebApp.war',
      type: 'war']
      ])
      }
    }
    stage ('DEV Deploy') {
      steps {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat9(credentialsId: '0e74d36e-4eeb-4963-b18f-cc3df3abe4b9', path: '', url: 'http://ec2-54-175-209-224.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
      }
    }

    stage ('QA Deploy') {
      steps {
      echo "deploying to QA Env "
      deploy adapters: [tomcat9(credentialsId: '63823fef-6f8e-4cf4-ab53-a4b6f8a94768', path: '', url: 'http://ec2-18-212-148-30.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
      }
    }


 stage ('Prod Deploy') {
      steps {
      echo "deploying to Prod Env "
      deploy adapters: [tomcat9(credentialsId: '043364ef-3091-48db-aaff-ef4781f38373', path: '', url: 'http://ec2-100-25-167-32.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
      }
    }

    stage ('Slack Notification') {
      steps {
        echo "deployed to DEV Env successfully"
        slackSend(channel:'PaulObalonye', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }
  }
}
