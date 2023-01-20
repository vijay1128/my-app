node{
   stage('SCM Checkout'){
     git 'https://github.com/vijay1128/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome = tool 'M3'   
      sh "${mvnHome}/bin/mvn clean package"
      sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'M3'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }

   stage('Build Docker Imager'){
   sh 'docker build -t vijayvs1128/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u vijayvs1128 -p ${dockerPassword}"
    }
   sh 'docker push vijayvs1128/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 18.134.3.203:8083"
   sh "docker tag vijayvs1128/myweb:0.0.2 18.134.3.203:8083/vijay:1.0.0"
   sh 'docker push 18.134.3.203:8083/vijay:1.0.0'
   }

   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest vijayvs1128/myweb:0.0.2' 
   }
}
}
