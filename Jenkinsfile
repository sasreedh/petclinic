node 
  {
	  def mvnHome = tool 'maven'
	  
	  stage ('workspace clean') {
	  cleanWs()	  
	  }
	  stage('Code Checkout')
	  {
		  git url: 'https://github.com/knagu/petclinic.git'
	  }	  
          stage('Build Stage')
	  {	   
	  sh "${mvnHome}/bin/mvn -B clean install package"
	  echo "Build Successful"
	  }
          stage('Test') {
	  sh "${mvnHome}/bin/mvn -B test"
	  echo "Tests successful"
	  }
          stage('Sonar Analysis')
	  {
		withSonarQubeEnv('sonar') 
		{
                sh "${mvnHome}/bin/mvn sonar:sonar"
			sh "sleep 10"
		} // SonarQube taskId is automatically attached to the pipeline context
	  }
          //stage('Deploy to artifactory'){
          //sh "curl -X PUT -u admin:admin123 -T /var/lib/jenkins/workspace/Petclinic/target/petclinic-1.${BUILD_NUMBER}.war 'http://localhost:8081/artifactory/JnJ/petclinic-1.${BUILD_NUMBER}.war'"
          //}
          stage('deploy to Dev') {
	  withCredentials( [usernamePassword( credentialsId: 'tomcat', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
	  sh "curl -u $USERNAME:$PASSWORD -T /var/lib/jenkins/workspace/Petclinic/target/petclinic-1.${BUILD_NUMBER}.war 'http://localhost:8082/manager/text/deploy?path=/Petclinic&update=true'"
	  }
		  echo "Deployment-successful"
	  }
          stage('slack notification'){
          slackSend channel: '#chatops', message: "started ${JOB_NAME} ${BUILD_NUMBER} (<${BUILD_URL}|Open>)"
          }
  }
