node{
   stage('SCM Checkout'){
     git 'https://github.com/kamesh20/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t kamesh20/myproject .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u kamesh20 -p ${dockerPassword}"
    }
   sh 'docker push kamesh20/myproject'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.234.29.71:8083"
   sh "docker tag kamesh20/myproject 13.234.29.71:8083/kamesh:1.0.1"
   sh 'docker push 13.234.29.71:8083/kamesh:1.0.1'
   }
    stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest kamesh20/myproject' 
   }
   }
}
