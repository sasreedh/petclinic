node 
  {
	  def mvnHome = tool 'maven'
	  
	  stage ('workspace clean') {
	  cleanWs()	  
	  }
	  stage('GitSCM')
	  {
		  git url: 'https://github.com/knagu/petclinic.git'
	  }	  
    	  stage('Build Stage')
	  {	   
	   sh "${mvnHome}/bin/mvn -B clean install package"
	   echo "Build Successful"
	  }
	  stage('Test'){		  
		  sh "${mvnHome}/bin/mvn -B test"
		  echo "Tests successful"
	  }
          stage('Sonar Analysis') 
	{
		withSonarQubeEnv('sonar') 
		{
		    def mvn = tool 'mvn'
			sh 'mvn clean package sonar:sonar'
			sh 'sleep 10'
		} // SonarQube taskId is automatically attached to the pipeline context
	}
          stage('deploy') {
		  withCredentials( [usernamePassword( credentialsId: 'tomcat', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
		  sh "curl -u $USERNAME:$PASSWORD -T /var/lib/jenkins/workspace/Petclinic/target/petclinic.war 'http://localhost:8081/manager/text/deploy?path=/Petclinic&update=true'"
		  }
		  echo "Deploy successful"
	  }	
	  	 	 
  }
